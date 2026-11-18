# 🛡️ 第 8 周：文件上传漏洞 (Upload-labs) 实战通关全记录

> **项目背景**：本项目为 Web 安全核心操作流程的第 8 周复盘，旨在通过 Upload-labs 靶场（Pass-01 至 Pass-21），深度解析文件上传漏洞的各类校验缺陷与绕过手法。
> **实验环境**：Kali Linux (Debian) / Apache / PHP 7.x / MariaDB
> **测试工具**：Burp Suite、中国蚁剑 (AntSword)

---

## 📌 漏洞核心认知
文件上传漏洞的本质是**服务器未能对用户上传的文件进行充分、安全的校验**。一旦恶意脚本（如 `.php`）成功上传并被解析执行，攻击者即可获取服务器的 Web 权限（GetShell）。
防御体系通常包含：前端 JS 校验、后端 MIME 校验、黑/白名单后缀校验、文件头（Magic Number）校验、二次渲染等。

---

## 🚀 闯关全记录与技术解析

### 第一阶段：基础校验与黑名单对抗 (Pass-01 ~ Pass-10)

#### 1. 前端 JS 与 MIME 绕过 (Pass-01 ~ Pass-02)
*   **Pass-01（前端 JS 校验）**：前端校验形同虚设。将木马命名为 `shell.jpg`，上传时用 Burp 拦截，在请求体中将 `filename="shell.jpg"` 修改为 `filename="shell.php"`。
*   **Pass-02（MIME 类型校验）**：后端检查 `Content-Type`。上传 `shell.php` 抓包，将请求头中的 `Content-Type: application/x-php` 修改为 `Content-Type: image/jpeg`。

#### 2. 黑名单与配置文件绕过 (Pass-03 ~ Pass-04)
*   **Pass-03（黑名单遗漏）**：后端拉黑了常见后缀，但遗漏了特殊别名。将木马重命名为 `shell.phtml`（或 `.php3`、`.php5`）直接上传。
*   **Pass-04（.htaccess 重写）**：黑名单未拦截 Apache 配置文件。先上传 `.htaccess` 文件（内容：`AddType application/x-httpd-php .jpg`），再上传图片木马 `shell.jpg`，从而让图片被当作 PHP 解析。

#### 3. 系统特性与源码级对抗 (Pass-05 ~ Pass-10)
*   **Pass-05（大小写绕过）**：尝试 `shell.pHp`。
*   **Pass-06 ~ Pass-09（Windows 特性）**：利用空格（`shell.php `）、点（`shell.php.`）、NTFS 文件流（`shell.php::$DATA`）等特性绕过。
    *   *💡 踩坑记录*：由于本靶场运行在 Linux (Debian) 下，系统不自动忽略空格和点，导致文件保存后带有多余字符，Apache 无法解析。此手法在真实 Windows 服务器下有效。
*   **Pass-10（双写与源码截取对抗）**：查看源码发现代码使用 `strrchr()` 提取最后一个后缀。尝试双写 `shell.pphphp`，虽然黑名单未拦截且文件落地，但 Apache 默认不解析 `.pphphp`。
    *   *破局*：使用黑名单遗漏的 `.php7` 或 `.phar` 后缀成功绕过。

---

### 第二阶段：白名单、图片马与渲染 (Pass-11 ~ Pass-16)

*   **Pass-11 ~ Pass-12（%00 截断）**：利用保存路径 `save_path` 可控，抓包修改为 `shell.php%00`。
    *   *⚠️ 环境限制*：该漏洞在 PHP 5.3.4 以后已被官方彻底修复，当前 PHP 7.x 环境下无法复现（理论掌握即可）。
*   **Pass-13 ~ Pass-15（文件头校验）**：后端使用 `getimagesize()` 校验。在 Kali 终端执行 `cat normal.jpg shell.php > shell.jpg` 制作图片马。上传成功后，必须配合 LFI（本地文件包含）才能触发执行。
*   **Pass-16（二次渲染）**：后端使用 GD 库重新生成图片。通过上传 GIF 图片马，下载渲染后的图片，用 010 Editor 定位未被破坏的像素区块，重新插入木马代码。

---

### 第三阶段：并发竞争与逻辑漏洞 (Pass-17 ~ Pass-21)

#### 1. 条件竞争 (Pass-17 ~ Pass-19)
*   **漏洞原理**：后端逻辑为“先保存文件 -> 检查后缀 -> 不合格则删除”。在删除前的瞬间执行文件，即可留下后门。
*   **实战破局**：由于 Burp 社区版 Intruder 限速，采用 **Python 多线程脚本** 进行并发上传与访问。
    *   *核心 Payload*：`<?php file_put_contents('1.php', '<?php @eval($_POST["cmd"]); ?>'); ?>`。
    *   *战术*：一边疯狂上传 `shell.php`，一边疯狂访问该路径。只要在文件被删除前成功访问，服务器就会执行代码，在目录下生成一个常驻的 `1.php`。

#### 2. 数组参数绕过 (Pass-20 ~ Pass-21)
*   **漏洞原理**：后端 `move_uploaded_file` 或拼接逻辑中，参数接收类型存在缺陷。
*   **Pass-20**：抓包在请求体末尾添加数组参数：
    ```http
    save_name[0]=shell.php/
    save_name[1]=