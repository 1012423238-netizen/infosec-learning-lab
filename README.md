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
├── .gitignore                             # 忽略规则（敏感信息、临时文件等）
├── .gitattributes                         # Git LFS 追踪规则
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
├── 03-study-plans/                        # 学习计划与总纲
│   ├── 网络安全自学完整计划.docx           # 整体学习规划
│   ├── 第2-4周自学操作流程.docx            # 第一阶段操作流程
│   └── WEB安全学习计划.md                  # Web 安全从零到实战详细计划
├── 04-operational-flows/                  # 核心操作流程（含大量截图）
│   ├── 第二部分-Web安全核心操作流程.docx    # 第二阶段操作流程（Git LFS 管理）
│   └── 第三部分-渗透测试实战操作流程.docx    # 第三阶段操作流程
└── 05-lab-reports/                        # 每周实操报告
    ├── 第五周-SQL注入.docx
    ├── 第六周-XSS注入.docx
    ├── 第七周-CSRF实验报告总结.docx
    ├── 第八周-文件上传漏洞（Upload-labs）.md
    └── 第八周-文件上传漏洞（Upload-labs）实战操作报告.docx
```

## 快速开始

本仓库中的 `04-operational-flows/第二部分-Web安全核心操作流程.docx` 体积较大，使用 Git LFS 托管。克隆时若未安装 Git LFS，该文件只会显示为一个指针文本，无法打开。

1. 安装 Git LFS（Windows 可下载官方发行版，或使用 `winget install Git.LFS`）。
2. 克隆仓库：

   ```bash
   git clone https://github.com/1012423238-netizen/infosec-learning-lab.git
   cd infosec-learning-lab
   ```

3. 拉取 LFS 托管的真实文件内容：

   ```bash
   git lfs install
   git lfs pull
   ```

4. 阅读入口：先看 `03-study-plans/WEB安全学习计划.md` 了解整体路线，再按周推进，对应查阅 `05-lab-reports/` 中的复盘报告。

## 使用方式

- **速查表**（`00-cheatsheets/`）：按阶段整理的命令与知识点速查，适合复习时快速翻阅。
- **学习计划**（`03-study-plans/`）：整体路线、时间安排与进度标注，作为自学的总纲。
- **操作流程**（`04-operational-flows/`）：每个阶段的详细实操步骤，含命令逐条讲解与截图。
- **实操报告**（`05-lab-reports/`）：按周记录特定漏洞的实验过程、绕过手法与踩坑总结。
- **Word 文档**（`.docx`）可用 Microsoft Word 或 WPS 打开；Markdown 文件可用任意编辑器阅读。

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
