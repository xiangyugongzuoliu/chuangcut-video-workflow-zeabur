# 创剪视频工作流 - Zeabur 部署模板

[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://zeabur.com/templates/TEMPLATE_CODE?referralCode=xiangyugongzuoliu)

> **注意**: 部署前请将上方链接中的 `TEMPLATE_CODE` 替换为实际的 Zeabur 模板 CODE

## 📋 项目介绍

**创剪视频工作流**是一个基于 Google Gemini AI 的全自动视频剪辑工具，能够智能分析视频内容、自动生成分镜脚本、AI 配音合成、云端音画同步处理，极大提升视频剪辑效率。

### ✨ 核心功能

- **📹 智能视频分析** - 使用 Gemini AI 深度理解视频内容，自动提取关键信息和要点
- **✂️ 自动生成分镜脚本** - AI 自动规划剪辑节奏和分镜方案，智能选择精彩片段
- **🎙️ AI 配音合成** - 集成 Fish Audio 高质量语音合成，自动生成自然流畅的解说音频
- **⚡ 云端音画同步处理** - 使用 NCA Toolkit 专业视频处理服务，自动调速、拼接、合成
- **🔄 断点续传** - 任务失败可从断点恢复，避免重复处理
- **📊 任务状态管理** - 实时监控处理进度，查看详细日志
- **🎨 多种解说风格** - 15+ 预设风格可选（科技、美食、旅游、教育等）

### 🏗️ 技术栈

- **前端**: Next.js 16 + React 19 + TypeScript
- **UI**: Tailwind CSS v4 + shadcn/ui
- **后端**: Node.js 20
- **数据库**: SQLite (better-sqlite3)
- **视频处理**: NCA Toolkit 云端服务
- **AI 服务**: Google Gemini + Fish Audio
- **部署**: Docker + Zeabur

---

## 🚀 快速开始

### 1. 获取授权码

部署前请联系供应商获取授权码 (LICENSE_KEY)。

授权码格式：`CCUT-XXXXXX-XXXXXXXXXXXX-XXXX`

**联系方式**：
- 邮箱: support@example.com
- 微信: example_wechat

### 2. 生成加密密钥

在本地终端运行以下命令生成加密密钥 (ENCRYPTION_KEY)：

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

输出示例：`1486fdd2753a4d4bcb6d3468bac183f8c514502e0f4db65a0c6165f2057e6fda`

### 3. 一键部署到 Zeabur

点击页面顶部的「Deploy on Zeabur」按钮，填写以下环境变量：

| 环境变量 | 说明 | 示例 |
|---------|------|------|
| `LICENSE_KEY` | 供应商提供的授权码（必填） | `CCUT-202511-6DTZR3XMUKND-E102` |
| `ENCRYPTION_KEY` | 加密密钥（必填，使用上一步生成的） | `1486fdd2753a4d4bcb6d3468bac183f8c514502e0f4db65a0c6165f2057e6fda` |

其他环境变量使用默认值即可。

### 4. 配置 API 密钥

部署完成后，访问部署的 URL，在 `/settings` 页面配置以下 API 密钥：

#### 必需配置

1. **Google Gemini**（二选一）
   - **Vertex AI 模式**（企业级，推荐）：
     - Service Account JSON
     - Project ID
     - Location (如 `us-central1`)
     - Model ID (如 `gemini-2.0-flash-exp`)
   - **AI Studio 模式**（个人用户，快速上手）：
     - API Key（从 [aistudio.google.com](https://aistudio.google.com) 获取）
     - Model ID (如 `gemini-2.0-flash-exp`)

2. **NCA Toolkit**（视频处理服务）
   - API Key

3. **Fish Audio**（语音合成）
   - API Key
   - Voice ID

#### 可选配置

4. **Google Cloud Storage**（存储最终成片）
   - Service Account JSON
   - Bucket Name

---

## 📖 使用指南

### 创建剪辑任务

1. 访问 `/jobs` 页面
2. 点击「创建新任务」
3. 填写任务信息：
   - **视频 URL**：输入公开可访问的视频链接（支持 HTTP/HTTPS）
   - **解说风格**：选择预设风格（如「科技解说」「美食探店」等）
   - **配置参数**：
     - 解说人声：原声 / 配音
     - 时长范围：30-60秒 / 60-90秒 等
     - 语速：慢速 / 正常 / 快速
4. 提交任务，系统自动处理

### 监控任务进度

任务处理分为 4 个阶段：

1. **视频分析** - 上传视频到 Gemini，生成分镜脚本
2. **分镜提取** - 使用 NCA 批量拆条
3. **音画同步** - 为每个分镜生成配音、调速、合成
4. **最终合成** - 拼接所有分镜，生成最终成片

可在任务详情页面查看：
- 实时进度
- 分镜预览
- 运行日志
- 最终成片下载链接

### 任务管理

- **暂停任务** - 暂停正在运行的任务
- **恢复任务** - 从断点继续处理
- **停止任务** - 终止任务（不可恢复）
- **重试任务** - 从失败的步骤重新开始

---

## 🔐 授权说明

### 授权验证机制

- 应用启动时自动验证授权码
- 验证失败将拒绝启动
- 授权信息包括：
  - 客户名称
  - 有效期限
  - 功能权限
  - 并发限制

### 授权过期提醒

- 剩余天数少于 30 天时，健康检查 API 会返回警告
- 授权过期后应用将无法启动
- 请提前联系供应商续期

### 安全保障

- 授权文件使用 AES-256-GCM 加密存储
- 解密密钥预置在 Docker 镜像中（用户不可见）
- 授权文件只读权限（chmod 400）
- 所有验证操作记录审计日志

---

## ⚙️ 环境变量参考

### 必需配置

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `LICENSE_KEY` | 授权码 | `CCUT-202511-6DTZR3XMUKND-E102` |
| `ENCRYPTION_KEY` | 加密密钥 | `1486fdd2753a4d4bcb6d3468bac183f8c514502e0f4db65a0c6165f2057e6fda` |

### 可选配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `DATABASE_URL` | SQLite 数据库路径 | `file:/data/db.sqlite` |
| `TEMP_DIR` | 临时文件目录 | `/temp` |
| `OUTPUT_DIR` | 输出文件目录 | `/output` |
| `LOGS_DIR` | 日志文件目录 | `/logs` |
| `NCA_REQUEST_TIMEOUT_MS` | NCA 请求超时（毫秒） | `300000` (5分钟) |
| `NCA_WEBHOOK_URL` | NCA Webhook 地址 | `https://www.google.com` |
| `LOG_LEVEL` | 日志级别 | `info` |

---

## 💡 常见问题

### Q: 授权码从哪里获取？
A: 请联系供应商获取授权码。授权码与客户绑定，包含有效期和功能限制。

### Q: 如何生成 ENCRYPTION_KEY？
A: 在本地运行：`node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`

### Q: 支持哪些视频格式？
A: 支持所有 NCA Toolkit 支持的格式（MP4、AVI、MOV 等）。建议使用 MP4 格式。

### Q: 视频处理需要多久？
A: 取决于视频长度和复杂度。一般 5-10 分钟的视频需要处理 10-20 分钟。

### Q: API 密钥存储在哪里？
A: 加密存储在 SQLite 数据库中（`/data/db.sqlite`），使用 AES-256-GCM 加密。

### Q: 如何查看详细日志？
A: 在任务详情页面的「运行日志」标签查看。日志存储在 SQLite 数据库的 `job_logs` 表中。

### Q: 授权过期后怎么办？
A: 联系供应商续期，更新 LICENSE_KEY 环境变量后重启服务。

### Q: 可以同时处理多个任务吗？
A: 可以，并发数取决于授权限制（如试用版 3 个，标准版 10 个，企业版 50 个）。

---

## 📚 文档链接

- [完整部署指南](./DEPLOYMENT_GUIDE.md)
- [使用手册](./USAGE.md)
- [常见问题](./FAQ.md)
- [API 参考](./API_REFERENCE.md)

---

## 🆘 技术支持

如有任何问题，请联系：

- **邮箱**: support@example.com
- **微信**: example_wechat
- **工作时间**: 周一至周五 9:00-18:00

---

## 📄 许可证

本模板仓库采用 MIT 许可证。

主应用（Docker 镜像）为商业软件，需要有效授权码才能使用。

---

**维护者**: 翔宇工作流
**最后更新**: 2025-11-19
