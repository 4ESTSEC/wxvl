# 微信公众号安全漏洞文章归档

[![自动更新](https://github.com/gelusus/wxvl/actions/workflows/update_today.yml/badge.svg)](https://github.com/gelusus/wxvl/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Language: Python](https://img.shields.io/badge/Language-Python-blue.svg)](https://python.org)
[![Platform: GitHub Actions](https://img.shields.io/badge/Platform-GitHub%20Actions-blue.svg)](https://github.com/features/actions)

> 本项目基于 [原版wxvl](https://github.com/20142995/wxvl) 进行扩展，新增了2022年4月至2025年4月期间的安全漏洞文章。

## 📋 目录

- [✨ 项目功能](#-项目功能)
- [📰 数据来源](#-数据来源)
- [🔍 内容筛选规则](#-内容筛选规则)
- [🛠️ 技术实现](#️-技术实现)
- [⚙️ 使用方法](#️-使用方法)
- [🤝 贡献指南](#-贡献指南)

---

## ✨ 项目功能

| 功能 | 说明 |
|------|------|
| 🤖 自动抓取 | 每日自动从多个渠道抓取安全漏洞相关文章 |
| 📄 格式转换 | 使用 [wechatmp2markdown](https://github.com/fengxxc/wechatmp2markdown) 将公众号文章转为 Markdown |
| 📁 知识库 | 按年月分类存储，建立本地知识库 |
| 🔄 持续更新 | GitHub Actions 每4小时自动执行 |
| 🏷️ 关键词过滤 | 智能识别 CVE、漏洞、POC/EXP 等关键词 |

## 📰 数据来源

数据来自以下渠道的公众号文章：

| 来源 | 说明 |
|------|------|
| [chainreactors/picker](https://github.com/chainreactors/picker) | 每日归档 |
| [BruceFeIix/picker](https://github.com/BruceFeIix/picker) | 每日归档 |
| [Doonsec](https://doonsec.com) | RSS 订阅源 |
| GitHub Issues | 从 Issues 中提取的公众号链接 |

## 🔍 内容筛选规则

系统会自动识别包含以下关键词的文章：

```
复现 | 漏洞 | CVE-\d+ | CNVD-\d+ | CNNVD-\d+ | XVE-\d+ | QVD-\d+
POC | EXP | 0day | 1day | nday | RCE | 代码执行 | 命令执行
```

## 🛠️ 技术实现

| 组件 | 技术 |
|------|------|
| 文章转换 | [wechatmp2markdown](https://github.com/fengxxc/wechatmp2markdown) |
| 文件命名 | 智能处理特殊字符，生成合规文件名 |
| 存储结构 | 按年月分类：`doc/yy-mm/` |
| 去重机制 | 通过 `data.json` 记录已处理链接，避免重复 |

## ⚙️ 使用方法

### 自动运行

- GitHub Actions 每 4 小时自动执行，无需手动干预
- 最新归档请查看 [doc/](https://github.com/4ESTSEC/wxvl/tree/main/doc) 目录

### 手动运行

```bash
# 克隆仓库
git clone https://github.com/4ESTSEC/wxvl.git
cd wxvl

# 抓取当日新文章
python run.py today

# 抓取历史文章（修改脚本后使用）
python run_history.py history
```

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！如发现公众号链接或有问题，请：

1. 提交 [Issue](https://github.com/4ESTSEC/wxvl/issues) 报告
2. Fork 本仓库并提交 PR
3. 直接在 [GitHub Issues](https://github.com/4ESTSEC/wxvl/issues) 中添加公众号链接

---

README optimized with [Gingiris README Generator](https://gingiris.github.io/github-readme-generator/)
