# CSRF 跨站请求伪造（Cross-Site Request Forgery）

> 一句话概述：利用受害者已登录的凭证（Cookie），伪造合法请求执行非预期操作，本质是「借刀杀人」。对应 CWE-352。

## 一、漏洞原理

CSRF 诱导已登录用户在不知情的情况下，向目标网站发起恶意请求。因为浏览器会自动携带 Cookie，服务器误以为是用户本人操作。

**成立条件**：仅凭 Cookie 鉴权（无额外 token 校验）；存在状态变更接口（改密码、转账、发帖）；缺少额外校验（Token / SameSite / Origin）。

## 二、Low 级别：基础攻击与浏览器防御机制

Low 级别下，改密码是无需校验的 GET 请求，服务器只依赖浏览器自动携带的 `PHPSESSID`：

```text
GET /DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change
```

构造恶意网页，用 `<img>` 静默发起请求：

```html
<img src="http://127.0.0.1/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none;">
```

**关键考点：为什么纯前端攻击在现代浏览器中会失败？** 用 Burp 抓包发现，浏览器跨域发起静默请求时自动剥离了 `PHPSESSID` Cookie。根本原因是现代浏览器默认开启 `SameSite=Lax` 策略——跨域静默请求（`<img>`、`<iframe>`）不具备顶级导航属性，浏览器拒绝携带 Cookie。

**漏洞真实存在的验证**：用 Burp 的 Repeater 手动构造带 Cookie 的请求并重放，服务器返回 200 OK、密码被成功修改，证明后端确实缺乏 CSRF 防护，只是被浏览器前端拦截了。

## 三、Medium 级别：Token 防御与 XSS 组合拳

Medium 级别引入 `user_token` 校验：每次请求页面生成随机 Token 嵌入表单，提交时必须带上。攻击者的钓鱼网页因同源策略无法跨域读取动态 Token，纯 CSRF 失效。

**绕过思路（XSS + CSRF 组合拳）**：若目标同时存在 XSS，可在同源环境用 JS 偷取 Token 并自动提交（同源执行不触发 SameSite 拦截）：

```html
<img src=x onerror="
fetch('/DVWA/vulnerabilities/csrf/')
.then(r=>r.text())
.then(h=>{
  let t=h.match(/name='user_token' value='([^']+)'/);
  if(t) {
    fetch('/DVWA/vulnerabilities/csrf/', {
      method:'POST',
      body:'password_new=pwned&password_conf=pwned&Change=Change&user_token='+t[1]
    });
  }
})">
```

## 四、底层安全机制：SameSite 与 HttpOnly

### SameSite Cookie 策略

| 取值 | 行为 |
|------|------|
| Strict | 绝对禁止跨站携带 Cookie（防 CSRF 最强，但影响跳转体验） |
| Lax（现代浏览器默认） | 仅允许 `<a>` 顶层导航 GET 请求携带；拦截 `<img>`/`<script>` 子资源请求 |
| None | 不设防，允许跨站携带（必须配合 `Secure` 仅在 HTTPS 生效） |

### HttpOnly 属性

服务器给 `PHPSESSID` 附加 `HttpOnly` 后，控制台执行 `document.cookie` 返回空字符串。作用：禁止 JS 读取 Cookie，有效防止 XSS 窃取凭证。

## 五、攻防对抗：SameSite=Lax 的绕过策略

若目标设置了 `SameSite=Lax`，攻击者的进阶策略：

1. **改成 GET + 顶层导航**：既然 Lax 允许点击链接跳转携带 Cookie，就把恶意 PoC 写成诱导点击的链接 `<a href="http://127.0.0.1/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change">点击领取皮肤</a>`（若目标只接受 POST 则失效）；
2. **XSS + CSRF 组合拳**：XSS 代码在目标同源环境执行，浏览器认为这是「同站请求」不触发 SameSite 跨站拦截，带着 Token 打 CSRF 畅通无阻；
3. **`_blank` 或 `window.open` 绕过**（历史遗留，早期浏览器存在逻辑漏洞，现代浏览器已修复）。

## 六、防御与修复方案

1. **CSRF Token**：使用一次性、随机的 Token（服务端生成，禁止通过 Cookie 传输，禁止放在 GET/URL 中）。
2. **SameSite Cookie**：设置 `SameSite=Strict` 或 `Lax`。
3. **校验 Referer / Origin 头**：拒绝非信任域的请求（`Origin: null` 不应加入白名单）。
4. **敏感操作二次验证**：改密码、转账要求输入原密码或验证码。
5. **不要用 GET 做状态变更操作**。

> 依据 OWASP CSRF Prevention Cheat Sheet：XSS 可以击溃所有 CSRF 缓解技术，但即使存在 XSS 风险，依赖 Cookie 认证的应用仍必须用 CSRF Token（同步器令牌模式）。

## 参考来源

- 《第七周-CSRF实验报告总结.docx》实战报告
- 《第二部分-Web安全核心操作流程.docx》第 7 周
- OWASP. "Cross-Site Request Forgery Prevention Cheat Sheet." *OWASP Cheat Sheet Series*, cheatsheetseries.owasp.org.
