# 第一阶段：基础补强（第 1-4 周）

> 目标：把已有知识从"使用视角"切换到"安全视角"，能分析一次完整 HTTP 请求。
> 环境：Kali Linux + DVWA + Burp Suite

---

## 第 2 周：Linux 安全基础

### 第 8 天：文件权限深入

#### 1. 看懂 ls -l 的输出

```bash
ls -l /etc/passwd
ls -l /etc/shadow
ls -l /bin/bash
```

输出示例：

```
-rw-r--r-- 1 root root  2847 /etc/passwd
-rw------- 1 root root  1234 /etc/shadow
-rwxr-xr-x 1 root root 1113504 /bin/bash
```

#### 2. 拆解权限位

以 `-rwxr-xr-x` 为例：

| 位置 | 字符 | 含义 |
|------|------|------|
| 第 1 位 | `-` | 文件类型（`-` 普通文件，`d` 目录，`l` 软链接） |
| 第 2-4 位 | `rwx` | 文件所有者（owner）的权限 |
| 第 5-7 位 | `r-x` | 文件所属组（group）的权限 |
| 第 8-10 位 | `r-x` | 其他人（other）的权限 |

权限数字对照：`r = 4`（读），`w = 2`（写），`x = 1`（执行）。所以 `rwxr-xr-x = 755`。

#### 3. 动手练习

```bash
# 创建测试文件
cd ~
echo "hello" > test.txt
ls -l test.txt
# 默认权限：-rw-rw-r--  664

# 加执行权限
chmod +x test.txt
ls -l test.txt
# 变成：-rwxrwxr-x  775

# 数字方式改权限
chmod 600 test.txt
ls -l test.txt
# 变成：-rw-------  只有所有者能读写

# 改所有者
sudo chown root:root test.txt
ls -l test.txt
```

#### 4. 目录权限的特殊性

目录的 `x` 权限表示能不能进入目录（`cd` 进去），不是执行什么东西。

#### 验收答案

- **rwxr-xr-x 每个部分**：所有者拥有读写执行权限，所属组和其他人只有读和执行权限
- **/etc/shadow 是 600**：只有所有者（root）能读写，防止普通用户读取密码哈希
- **chmod 755 后的结果**：`-rwxr-xr-x`，所有者拥有全部权限，所属组和其他人只有读和执行

---

### 第 9 天：SUID/SGID 权限

#### 1. 什么是 SUID

SUID = Set User ID。普通程序运行时用的是运行者的权限。但如果一个程序设置了 SUID，那么不管谁运行它，程序都以文件所有者的权限运行。

最典型的例子：`passwd` 命令。

```bash
ls -l /usr/bin/passwd
# 输出：-rwsr-xr-x 1 root root ... /usr/bin/passwd
# 注意那个 s —— 这就是 SUID
```

**为什么 passwd 需要 SUID？** 普通用户要改密码需要写 `/etc/shadow`，但 `/etc/shadow` 只有 root 能写。所以 passwd 命令设置了 SUID，不管谁运行，都以 root 权限运行。

#### 2. 找出系统中所有 SUID 文件

```bash
sudo find / -perm -4000 -type f 2>/dev/null
```

常见的 SUID 程序：`/usr/bin/passwd`、`/usr/bin/sudo`、`/usr/bin/su`、`/usr/bin/mount`。

#### 3. SUID 的安全风险

危险的 SUID = 攻击者可以提权的入口。如果一个 SUID 程序是 root 拥有的，而且它能执行命令（比如 `find`、`vim`、`bash`），那普通用户运行它就能拿到 root 权限。

#### 4. 动手做 SUID 提权实验

```bash
# 1. 复制 bash 到 /tmp
cp /bin/bash /tmp/mybash

# 2. 给 mybash 设置 SUID，所有者是 root
chmod u+s /tmp/mybash
ls -l /tmp/mybash
# 输出：-rwsr-xr-x 1 root root ... /tmp/mybash

# 3. 切到普通用户
su kali

# 4. 运行带 SUID 的 bash
/tmp/mybash -p

# 5. 看看现在是谁
whoami
# 输出：root！
# -p 参数表示"保持有效用户 ID"，这是关键
```

#### 验收答案

- **SUID 是什么**：Linux 权限特殊位，只对可执行文件生效，以文件主人的身份来执行程序
- **找 SUID 文件**：`sudo find / -perm -4000 -type f 2>/dev/null`
- **为什么是提权入口**：如果文件属主是 root，任何普通用户执行这个程序，进程都会临时拥有 root 权限。提权的根本来源：普通用户 → 临时 root 身份

---

### 第 10 天：iptables 防火墙

#### 1. 查看当前状态

```bash
sudo iptables -L -n
```

#### 2. iptables 的三个链

| 链名 | 作用 |
|------|------|
| `INPUT` | 进入本机的流量（别人访问本机） |
| `OUTPUT` | 本机向外发出的包 |
| `FORWARD` | 穿过本机转发的包（本机做路由器才用到） |

#### 3. 配置规则

```bash
# 1. 先允许 SSH（22 端口）！！！不然远程连不上了
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# 2. 允许本机回环
sudo iptables -A INPUT -i lo -j ACCEPT

# 3. 允许已经建立的连接
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 4. 拒绝其他所有入站流量
sudo iptables -A INPUT -j DROP

# 5. 查看规则
sudo iptables -L -n -v
```

#### 4. 删除规则（恢复默认）

```bash
sudo iptables -F
sudo iptables -L -n
```

#### 验收答案

- **只允许 SSH 的配置**：

```bash
# 1. 允许访问本机 22 端口（SSH）
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# 2. 设置 INPUT 链默认策略为 DROP（所有没匹配到的包全部丢弃）
iptables -P INPUT DROP
```

- **为什么先允许 SSH 再设置默认拒绝**：iptables 的默认策略修改立即生效，如果先设置默认 DROP，此时 SSH 放行规则还不存在，现有 SSH 连接会被直接断开，服务器远程锁死。因此必须先添加允许 SSH 的规则，再配置默认拒绝。

---

### 第 11 天：日志分析

#### 1. 日志文件位置

```bash
ls /var/log/
```

| 文件 | 内容 |
|------|------|
| `/var/log/auth.log` | 登录认证日志（SSH、su、sudo） |
| `/var/log/auth.log.1` | 轮转旧认证日志 |
| `/var/log/syslog` | 系统全局日志 |
| `/var/log/apache2/access.log` | Apache 访问日志 |
| `/var/log/apache2/access.log.1` | 归档历史访问日志 |
| `/var/log/apache2/access.log.2.gz` | 压缩归档日志 |

> **注意**：新版 Kali 日志带 ISO 时间戳，读取时可能提示二进制，需要加 `-a` 参数强制按文本解析。

#### 2. 分析登录日志

```bash
# 查看所有 sshd 相关日志
grep -a "sshd" /var/log/auth.log

# 只看密码失败记录
grep -a 'sshd.*Failed password' /var/log/auth.log

# 统计失败 IP（次数降序）
grep -a 'sshd.*Failed password' /var/log/auth.log \
  | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' \
  | sort | uniq -c | sort -rn

# 看成功登录
sudo grep -a "Accepted" /var/log/auth.log
```

> **实训结果**：检索到 3 次 SSH 密码失败记录，攻击源 IP：127.0.0.1（本机回环地址，本地模拟爆破），目标账号 root，无外部 IP 爆破。

#### 3. 分析 Apache 访问日志

```bash
# 最近 20 条访问记录
sudo tail -20 /var/log/apache2/access.log.1

# 统计访问 IP（次数从高到低）
awk '{print $1}' /var/log/apache2/access.log.1 | sort | uniq -c | sort -rn

# 查找 sqlmap、union select 注入特征
grep -E "sqlmap|union.*select" /var/log/apache2/access.log.1

# 统计 HTTP 状态码
awk '{print $9}' /var/log/apache2/access.log.1 | sort | uniq -c | sort -rn

# 查找 404 请求（目录爆破痕迹）
grep " 404 " /var/log/apache2/access.log.1

# 查看 gz 压缩包里的旧日志
zcat /var/log/apache2/access.log.2.gz | grep -E "sqlmap|union.*select"
```

#### 异常请求判断标准

- 单个 IP 大量高频访问 → 目录扫描 / 爬虫
- URL 包含 `union select`、`and 1=1` → SQL 注入探测
- User-Agent 包含 `sqlmap` → sqlmap 工具扫描
- 大量 404 状态码请求 → 目录爆破

---

## 第 3 周：Python 安全脚本入门

### 第 12 天：requests 库

> requests 是 Python 的 HTTP 请求库，用来模拟浏览器发送网络请求，不用自己手写底层 socket。

#### 1. 安装

```bash
pip3 install requests
```

#### 2. 简单 GET 请求

```python
import requests

# 发 GET 请求
response = requests.get("http://httpbin.org/get")

# 看状态码
print("状态码:", response.status_code)

# 看响应头
print("响应头:", response.headers)

# 看响应内容
print(response.text)
```

#### 3. 带参数的 GET 请求

```python
data = {"name": "admin", "age": 20}
response = requests.get("http://httpbin.org/get", params=data)
print(response.text)
```

#### 4. POST 请求（表单提交）

```python
data = {"username": "admin", "password": "123456"}
response = requests.post("http://httpbin.org/post", data=data)
print(response.text)
```

#### 5. 带请求头和 Cookie

```python
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
    "Accept": "text/html"
}
cookies = {"session_id": "abc123"}

response = requests.get(
    "http://httpbin.org/get",
    headers=headers,
    cookies=cookies
)
```

#### 验收答案

- **GET vs POST**：`requests.get()` 发送 GET 请求，参数通过 `params` 追加在 URL 后面；`requests.post()` 发送 POST 请求，表单数据放在请求体，不会显示在 URL，常用于登录提交
- **headers 和 cookies**：`headers` 自定义 HTTP 请求头，修改 User-Agent，避免网站识别是 Python 脚本；`cookies` 携带会话 Cookie，模拟登录后的用户身份
- **response 对象**：`status_code` 是 HTTP 状态码（200=成功，404=页面不存在，401=未授权，500=服务器错误）；`headers` 是响应头字典；`text` 是网页返回的文本内容

---

### 第 13 天：目录扫描器

#### 1. 原理

拿一个字典，逐个访问 `http://目标/字典里的每一个词`，看返回状态码是不是 200。如果是 200，说明这个目录存在。

#### 2. 准备字典

```bash
nano dirs.txt
```

```
admin
login
phpmyadmin
wp-admin
uploads
images
docs
backup
config
test
```

#### 3. 扫描脚本

```python
import requests

# 目标地址，注意末尾带 /
target = "http://127.0.0.1/DVWA/"

# 读取字典文件，splitlines() 自动去掉每行末尾换行符
with open("dirs.txt", "r") as f:
    dirs = f.read().splitlines()

print(f"开始扫描 {target}，共 {len(dirs)} 个目录...\n")

# 循环遍历字典里每一个目录名
for dir_name in dirs:
    url = target + dir_name
    try:
        # 发送 GET 请求，timeout=3 秒超时，防止卡死
        response = requests.get(url, timeout=3)
        if response.status_code == 200:
            print(f"[+] 发现目录: {url}")
        elif response.status_code == 403:
            print(f"[!] 禁止访问: {url}")
    except Exception as e:
        # 网络超时、连接失败直接跳过
        pass

print("\n扫描完成！")
```

#### 4. 运行

```bash
sudo systemctl start apache2
python3 dir_scan.py
```

#### 验收答案

- **为什么要用字典爆破**：网站隐藏目录无法通过首页链接发现；字典内置 Web 系统常见目录名称，自动批量尝试访问，发现后台、上传目录等敏感路径，属于 Web 信息收集手段

---

### 第 14 天：正则表达式提取信息

#### 1. 基础正则语法

| 符号 | 含义 | 例子 |
|------|------|------|
| `.` | 任意字符 | `a.c` 匹配 abc、a1c |
| `*` | 前面的字符出现 0 次或多次 | `ab*c` 匹配 ac、abc、abbbc |
| `+` | 前面的字符出现 1 次或多次 | `ab+c` 匹配 abc、abbbc |
| `?` | 前面的字符出现 0 次或 1 次 | `ab?c` 匹配 ac、abc |
| `\d` | 数字 | `\d+` 匹配 123、456 |
| `\w` | 字母数字下划线 | `\w+` 匹配 admin、user123 |
| `()` | 分组 | 提取括号里的内容 |

#### 2. Python re 库基础

```python
import re

text = "我的手机号是 13812345678，邮箱是 test@example.com"

# search：找到第一个匹配
phone = re.search(r"\d{11}", text)
if phone:
    print("手机号:", phone.group())

# findall：找到所有匹配
emails = re.findall(r"\w+@\w+\.\w+", text)
print("邮箱:", emails)
```

#### 3. 从网页提取链接

```python
import requests
import re

response = requests.get("http://127.0.0.1/DVWA/")
html = response.text

links = re.findall(r'href="(.*?)"', html)

print("找到的链接：")
for link in links:
    print(f"  {link}")
```

#### 验收答案

- **search vs findall**：`re.search` 查第一个匹配；`re.findall` 查全部
- **常用正则**：手机号 `r"\d{11}"`，IP `r"\d+\.\d+\.\d+\.\d+""`，邮箱 `r"\w+@\w+\.\w+"`
- **提取链接**：`href="(.*?)"`

---

### 第 15 天：第三周复盘 + 综合练习

```python
import requests
import re

target = "http://127.0.0.1/DVWA/"

print(f"=== 对 {target} 的初步信息收集 ===\n")

# 1. 看响应头
r = requests.get(target)
print("[+] 服务器信息：")
print(f"    服务器: {r.headers.get('Server', '未知')}")
print(f"    状态码: {r.status_code}")

# 2. 提取页面里的链接
print("\n[+] 页面中的链接：")
links = re.findall(r'href="(.*?)"', r.text)
for link in links[:10]:
    print(f"    {link}")

# 3. 扫常见后台目录
print("\n[+] 常见目录扫描：")
common_dirs = ["admin", "login", "config", "uploads", "phpmyadmin"]
for d in common_dirs:
    try:
        dr = requests.get(target + d, timeout=2)
        if dr.status_code == 200:
            print(f"    [+] 发现: {target}{d}")
    except:
        pass

print("\n=== 信息收集完成 ===")
```

---

## 第 4 周：Web 前端与浏览器机制

### 第 16 天：浏览器开发者工具

#### 1. 打开方式

打开 DVWA 登录页，按 `F12` 或 `Ctrl + Shift + I`。

| 面板 | 干什么用的 |
|------|-----------|
| **Elements** | 看/改页面的 HTML 结构 |
| **Console** | 执行 JavaScript、看报错 |
| **Network** | 看所有网络请求 |
| **Application** | 看 Cookie、LocalStorage |

#### 2. Elements 面板练习

1. 找到用户名输入框
2. 双击把 `value="admin"` 改成 `value="hacked"`
3. 看页面 —— 输入框里的文字变了！

> 关键理解：你改的是浏览器里的页面，不是服务器上的。刷新就恢复了。

#### 3. Console 面板练习

```javascript
// 1. 改页面标题
document.title = "我被改了！";

// 2. 弹出一个框
alert("Hello!");

// 3. 修改页面内容
document.body.innerHTML = "<h1>被 XSS 了</h1>";

// 4. 读 Cookie
document.cookie;
```

---

### 第 17 天：Cookie 深入

#### 1. 查看 Cookie

F12 → Application → Cookies → `http://127.0.0.1`

#### 2. Cookie 属性及安全意义

| 属性 | 含义 | 安全意义 |
|------|------|---------|
| Name | Cookie 名字 | |
| Value | Cookie 值 | |
| Domain | 哪个网站能读这个 Cookie | |
| Path | 哪个路径下能读 | |
| Expires | 什么时候过期 | Session Cookie 关闭浏览器就没了 |
| **HttpOnly** | JS 能不能读这个 Cookie | 有了它，XSS 就偷不到 Cookie |
| **Secure** | 只有 HTTPS 才传这个 Cookie | 防止明文传输被抓 |
| **SameSite** | 跨站请求带不带这个 Cookie | 防 CSRF |

#### 3. 动手验证

在 Console 里执行 `document.cookie`，看看能读到什么。如果一个 Cookie 设置了 HttpOnly，JS 就读不到 —— 这就是 HttpOnly 的作用：防 XSS 偷 Cookie。

---

### 第 18 天：同源策略

#### 1. 什么是同源

两个 URL "同源" 需要三个都一样：

1. **协议**：http vs https
2. **域名**：www.baidu.com vs baidu.com
3. **端口**：80 vs 8080

#### 2. 判断示例

| URL 1 | URL 2 | 同源吗？ |
|-------|-------|---------|
| `http://example.com` | `http://example.com/about` | ✅ 是（路径不同没关系） |
| `http://example.com` | `https://example.com` | ❌ 不是（协议不同） |
| `http://example.com` | `http://api.example.com` | ❌ 不是（域名不同） |
| `http://example.com` | `http://example.com:8080` | ❌ 不是（端口不同） |

#### 3. 同源策略限制了什么

不同源的网站之间：不能读对方的 Cookie、不能读对方的 LocalStorage、不能通过 JS 读取对方的 DOM、Ajax 请求默认跨域被限制。

---

### 第 19 天：JavaScript DOM 操作

#### 1. 什么是 DOM

DOM（Document Object Model）就是浏览器把 HTML 解析成的一棵树。你可以用 JS 操作这棵树，增删改查页面元素。

#### 2. 常用 DOM 操作

```javascript
// 1. 找到一个元素
document.getElementById("login");

// 2. 找到所有 <a> 标签
document.getElementsByTagName("a");

// 3. 修改元素内容
document.querySelector("h1").innerText = "被我改了";

// 4. 修改元素样式
document.querySelector("h1").style.color = "red";

// 5. 新建一个元素
let p = document.createElement("p");
p.innerText = "我是新加的";
document.body.appendChild(p);

// 6. 删除一个元素
document.querySelector("p").remove();
```

#### 3. XSS 的本质

XSS（跨站脚本攻击）的本质就是：**攻击者把恶意 JS 代码注入到页面里，让受害者的浏览器执行**。

#### 4. 动手做 XSS 测试

1. 打开 DVWA，把安全等级设为 Low
2. 进入 XSS (Reflected) 页面
3. 在输入框里输入：

```html
<script>alert(document.cookie)</script>
```

4. 点击提交，你会看到弹窗，显示你的 Cookie

这就是最基础的 XSS 攻击。因为网站没有过滤用户输入，直接把你输入的内容输出到了页面上。

---

### 第 20 天：第一阶段总验收

#### 验收 Checklist

- [ ] 能用 Wireshark 抓包并分析 HTTP 请求
- [ ] 能用 Burp 抓 HTTPS 包
- [ ] 能解释 DNS 解析流程
- [ ] 能写 TCP 客户端和服务端
- [ ] 能写简易端口扫描器
- [ ] 能看懂 Linux 文件权限、SUID
- [ ] 能配置 iptables 防火墙规则
- [ ] 能分析 auth.log 找暴力破解
- [ ] 能用 requests 发 GET/POST 请求
- [ ] 能写目录扫描器
- [ ] 能用正则提取邮箱、IP、链接
- [ ] 能熟练使用浏览器开发者工具
- [ ] 能说出 Cookie 各属性的安全意义
- [ ] 能解释同源策略
- [ ] 能在 DVWA 里打出 XSS 弹窗

全部打勾，第一阶段（4 周）就完成了！接下来进入第二阶段：Web 漏洞逐个攻破。
