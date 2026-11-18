# Infosec Learning Lab

一个用于沉淀网络安全自学过程的实践仓库。它记录了我从零基础到 Web 渗透测试的完整学习路径，内容涵盖学习计划、核心操作流程与每周实操报告。

## 项目简介

本仓库把我在网络安全自学期间产出的资料集中归档，方便持续复盘与知识沉淀。学习主线参考 ReAbout 维护的《WEB 安全手册》，整体路线划分为三个阶段，从基础补强逐步推进到 Web 漏洞利用，再到完整的渗透测试实战（ReAbout，"web-sec: WEB 安全手册"）。

整个学习过程遵守一个基本原则：只在授权的靶场和自建环境中练习，绝不触碰未授权的目标。

## 学习路线总览

| 阶段 | 时间 | 核心目标 | 对应速查表 |
|------|------|---------|-----------|
| 第一阶段：基础补强 | 第 1–4 周 | 从"使用视角"切换到"安全视角"，能够分析一次完整的 HTTP 请求 | `00-cheatsheets/week1-4-basic.md` |
| 第二阶段：Web 安全核心 | 第 5–12 周 | 掌握 OWASP Top 10 漏洞的原理与利用方法，熟练使用 Burp Suite | `00-cheatsheets/week5-12-web-security.md` |
| 第三阶段：渗透测试实战 | 第 13–20 周 | 独立完成从信息收集到权限提升的全流程 | `00-cheatsheets/week13-20-pentest.md` |

## 仓库目录结构

```text
infosec-learning-lab/
├── README.md                              # 本文件
├── 00-cheatsheets/                        # 三阶段速查表
│   ├── week1-4-basic.md                   # 第一阶段：Linux/Python/浏览器安全基础
│   ├── week5-12-web-security.md           # 第二阶段：Web 漏洞逐个攻破
│   └── week13-20-pentest.md               # 第三阶段：渗透测试实战
├── 01-network-fundamentals/               # 网络基础与校园网规划设计
│   ├── README.md
│   ├── images/                            # 拓扑与配置截图
│   └── 校园网规划与设计.zip                # 组网设计附件
├── 02-web-security-lab/                   # DVWA 靶场搭建与实操笔记
│   ├── README.md                          # DVWA 安装教程
│   └── docs/images/                       # 安装过程截图
├── 03-study-plans/                        # 学习计划
│   └── 第2-4周自学操作流程.md             # 基础阶段操作流程
├── 05-lab-reports/                        # 每周实操报告
│   ├── 第五周-SQL注入.md
│   ├── 第六周-XSS注入.md
│   ├── 第七周-CSRF实验报告总结.md
│   └── 第八周-文件上传漏洞（Upload-labs）.md
└── 06-attack-notes/                       # 攻击分类笔记（按漏洞类型）
    ├── 01-sql-injection.md
    ├── 02-xss.md
    ├── 03-csrf.md
    └── 04-file-upload.md
```

> 学习笔记持续更新中：文件包含、命令注入、SSRF、XXE 等后续攻击笔记，以及渗透测试实战操作流程，将在练习完成后陆续补充。

## 快速开始

```bash
git clone https://github.com/1012423238-netizen/infosec-learning-lab.git
cd infosec-learning-lab
```

阅读入口：先看 `00-cheatsheets/` 了解三阶段学习速查，再按漏洞类型查阅 `06-attack-notes/` 中的攻击分类笔记。

## 使用方式

- **速查表**（`00-cheatsheets/`）：按阶段整理的命令与知识点速查，适合复习时快速翻阅。
- **学习计划**（`03-study-plans/`）：基础阶段操作流程。
- **每周实操报告**（`05-lab-reports/`）：按周记录 SQL 注入、XSS、CSRF、文件上传的实操与踩坑总结。
- **攻击分类笔记**（`06-attack-notes/`）：按漏洞类型整理的攻击笔记，含原理、利用手法、实战踩坑与防御方案。
- **网络基础**（`01-network-fundamentals/`）：网络基础知识与校园网规划设计。
- **靶场搭建**（`02-web-security-lab/`）：DVWA 靶场搭建与实操笔记。
- **Markdown 文件**可用任意编辑器阅读。

## 靶场环境与工具

**练习靶场**

- Kali Linux：主操作环境
- DVWA：Web 漏洞综合练习靶场
- sqli-labs：SQL 注入专项练习
- Upload-labs：文件上传漏洞专项练习
- Vulnhub：渗透测试靶机
- 攻防世界：CTF Web 方向练习

**常用工具**

| 工具 | 用途 |
|------|------|
| Burp Suite | Web 抓包改包、漏洞测试 |
| Nmap | 端口扫描、服务识别 |
| sqlmap | 自动化 SQL 注入 |
| Dirsearch | Web 目录扫描 |
| Wireshark | 网络抓包分析 |
| Python 3 | 安全脚本编写 |
| 中国蚁剑 | Webshell 连接管理 |

## 贡献指南

本仓库以个人学习记录为主，同时欢迎提出改进建议。参与方式如下：

1. Fork 本仓库到自己的账号。
2. 基于 `main` 分支创建功能分支，例如 `git checkout -b docs/补充XSS绕过`。
3. 提交修改时使用语义化提交信息，例如 `docs: 补充 XSS 绕过手法`。
4. 发起 Pull Request，并简要说明修改内容与动机。

提交前请确认：不包含任何密钥、凭证或未授权目标的敏感信息；大文件（超过约 10 MB）优先使用 Git LFS 追踪。

## 合法边界

> 本仓库中的全部技术与命令仅用于授权靶场和自建环境的学习研究。严禁对任何未授权的网站或系统进行扫描、渗透或攻击，由此产生的一切后果由实施者自行承担。

## 参考资源

ReAbout. "web-sec: WEB 安全手册." *GitHub*, github.com/ReAbout/web-sec.
