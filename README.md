# Infosec Learning Lab

网络安全自学实践仓库，记录从零基础到 Web 渗透测试的完整学习路径与实操笔记。

## 学习路线总览

| 阶段 | 时间 | 核心目标 | 目录 |
|------|------|---------|------|
| 第一阶段：基础补强 | 第 1-4 周 | 从"使用视角"切换到"安全视角"，能分析一次完整 HTTP 请求 | `00-cheatsheets/week1-4-basic.md` |
| 第二阶段：Web 安全核心 | 第 5-12 周 | 掌握 OWASP Top 10 漏洞原理与利用方法，熟练使用 Burp Suite | `00-cheatsheets/week5-12-web-security.md` |
| 第三阶段：渗透测试实战 | 第 13-20 周 | 从信息收集到权限提升全流程独立完成 | `00-cheatsheets/week13-20-pentest.md` |

## 仓库结构

```
infosec-learning-lab/
├── README.md                          # 本文件
├── 00-cheatsheets/                    # 操作流程速查表
│   ├── week1-4-basic.md              # 第一阶段：Linux/Python/浏览器安全基础
│   ├── week5-12-web-security.md      # 第二阶段：Web 漏洞逐个攻破
│   └── week13-20-pentest.md           # 第三阶段：渗透测试实战
├── 02-web-security-lab/               # DVWA 靶场搭建与实操笔记
│   ├── README.md                     # DVWA 安装教程
│   └── docs/images/                  # 安装截图
└── docs/                              # 文档资源
```

## 靶场环境

- **Kali Linux**：主操作环境
- **DVWA**：Web 漏洞练习靶场
- **sqli-labs**：SQL 注入专项练习
- **Upload-labs**：文件上传专项练习
- **Vulnhub**：渗透测试靶机
- **攻防世界**：CTF Web 方向练习

## 工具清单

| 工具 | 用途 |
|------|------|
| Burp Suite | Web 抓包改包、漏洞测试 |
| Nmap | 端口扫描、服务识别 |
| sqlmap | 自动化 SQL 注入 |
| Dirsearch | Web 目录扫描 |
| Wireshark | 网络抓包分析 |
| Python 3 | 安全脚本编写 |
| 中国蚁剑 | Webshell 连接管理 |

## 合法边界

> 只在授权的靶场和自己的机器上练习！
> 严禁对任何未授权的网站/系统进行扫描、渗透、攻击。
