# 创剪视频工作流 - Zeabur 部署模板

[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://zeabur.com/templates/TEMPLATE_CODE?referralCode=xiangyugongzuoliu)

> **注意**: 部署前请将上方链接中的 `TEMPLATE_CODE` 替换为实际的 Zeabur 模板 CODE

## 📋 项目介绍

**创剪视频工作流**是一个基于 Google Gemini AI 的全自动视频剪辑工具，能够智能分析视频内容、自动生成分镜脚本、AI 配音合成、云端音画同步处理，极大提升视频剪辑效率。

### ✨ 核心功能

- **📹 智能视频分析** - 使用 Gemini AI 深度理解视频内容
- **✂️ 自动生成分镜脚本** - AI 自动规划剪辑节奏和分镜方案
- **🎙️ AI 配音合成** - Fish Audio 高质量语音生成
- **⚡ 云端音画同步处理** - NCA Toolkit 专业视频处理
- **🔄 断点续传** - 任务失败可从断点恢复
- **📊 任务状态管理** - 实时监控处理进度
- **🎨 多种解说风格** - 15+ 预设风格可选

### 🏗️ 技术栈

- **前端**: Next.js 16 + React 19 + TypeScript
- **UI**: Tailwind CSS v4 + shadcn/ui
- **后端**: Node.js 20
- **数据库**: SQLite (better-sqlite3)
- **视频处理**: NCA Toolkit 云端服务
- **AI 服务**: Google Gemini + Fish Audio
- **部署**: Docker + Zeabur

---

## 🚀 一键部署

点击页面顶部的「Deploy on Zeabur」按钮，按提示填写环境变量即可完成部署。

部署完成后，访问 `/settings` 页面配置 API 密钥（Gemini、NCA、Fish Audio 等）。

---

## 📖 使用说明

### 创建视频任务

1. 访问 `/jobs` 页面
2. 点击「创建新任务」
3. 输入视频 URL 和选择解说风格
4. 提交任务，系统自动处理

### 监控进度

任务处理分为 4 个阶段：视频分析 → 分镜提取 → 音画同步 → 最终合成

可在任务详情页查看实时进度、分镜预览和运行日志。

---

## 📄 许可证

本模板仓库采用 MIT 许可证。

主应用（Docker 镜像）为商业软件，需要有效授权码才能使用。

---

**作者**: 翔宇工作流
**最后更新**: 2025-11-19
