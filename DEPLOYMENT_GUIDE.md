# 创剪视频工作流 - Zeabur 部署指南

本文档说明如何使用此公开模板仓库部署创剪视频工作流到 Zeabur 平台。

---

## 📦 仓库结构

```
chuangcut-video-workflow-zeabur/
├── template.yaml        # Zeabur 模板配置文件
├── README.md           # 用户部署说明
├── .env.example        # 环境变量模板
├── .gitignore         # Git 忽略文件
├── DEPLOYMENT_GUIDE.md # 本文档（供应商使用）
└── assets/            # 图标和封面图片
    ├── icon.png
    └── cover.png
```

---

## 🔑 关键说明

### 1. 本仓库是公开的配置仓库

- **仅包含**：配置文件、文档、部署说明
- **不包含**：任何源代码、敏感信息
- **目的**：满足 Zeabur 要求的公开仓库条件

### 2. 实际应用来自 Docker 镜像

- **镜像地址**: `xiangyugongzuoliu/chuangcut-video-workflow:latest`
- **镜像源**：私有仓库 `chuangcut-video-workflow` 通过 GitHub Actions 自动构建
- **更新方式**：推送新代码 → 自动构建 → 推送镜像 → Zeabur 自动拉取

### 3. 授权系统集成

- **授权文件**：`config/licenses.yaml.enc`（加密，预置在镜像中）
- **解密密钥**：`LICENSE_ENCRYPTION_KEY`（通过 GitHub Secrets 注入镜像，用户不可见）
- **用户配置**：仅需提供 `LICENSE_KEY`（授权码）
- **验证机制**：应用启动时自动验证，失败拒绝启动

---

## 🚀 模板提交流程

### 步骤 1: 安装 Zeabur CLI

```bash
# macOS (arm64)
wget https://github.com/zeabur/cli/releases/download/v0.5.4/zeabur_Darwin_arm64.tar.gz
tar -xzf zeabur_Darwin_arm64.tar.gz
mkdir -p ~/.local/bin
mv zeabur ~/.local/bin/
chmod +x ~/.local/bin/zeabur

# 添加到 PATH（如果尚未添加）
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 步骤 2: 登录 Zeabur

```bash
zeabur auth login
# 按提示在浏览器完成登录
```

### 步骤 3: 提交模板

首次提交：

```bash
cd /path/to/chuangcut-video-workflow-zeabur
zeabur template create -f template.yaml
```

输出示例：

```
Template "chuangcut-video-workflow" (https://zeabur.com/templates/ABC123) created
```

**重要**：记录模板 CODE（如 `ABC123`），用于后续更新模板和生成部署链接。

### 步骤 4: 更新 README.md 中的部署按钮

将 README.md 中的 `TEMPLATE_CODE` 替换为实际的模板 CODE：

```markdown
[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://zeabur.com/templates/ABC123?referralCode=xiangyugongzuoliu)
```

### 步骤 5: 提交更改到 GitHub

```bash
git add README.md
git commit -m "更新 Zeabur 部署链接"
git push origin main
```

---

## 🔄 更新模板

当需要修改模板配置时：

```bash
# 1. 修改 template.yaml
vim template.yaml

# 2. 更新到 Zeabur
zeabur template update -c ABC123 -f template.yaml

# 3. 提交到 Git
git add template.yaml
git commit -m "更新模板配置"
git push origin main
```

---

## 🐳 更新 Docker 镜像

镜像更新通过私有仓库的 GitHub Actions 自动完成：

### 自动构建流程

1. 在私有仓库 `chuangcut-video-workflow` 推送代码到 `main` 分支
2. GitHub Actions 自动触发 `.github/workflows/docker-build-push.yml`
3. 构建多平台镜像（linux/amd64, linux/arm64）
4. 推送到 Docker Hub: `xiangyugongzuoliu/chuangcut-video-workflow:latest`
5. Zeabur 自动拉取最新镜像（用户需手动重启服务）

### 手动构建流程

```bash
cd /path/to/chuangcut-video-workflow

# 设置授权加密密钥
export LICENSE_ENCRYPTION_KEY=your-64-char-hex-key

# 执行发布脚本
./scripts/publish-docker.sh
```

---

## 🌐 部署架构

```
┌─────────────────────────────────┐
│  私有源码仓库 (Private)          │
│  chuangcut-video-workflow       │
│  - 完整源代码                    │
│  - 授权系统                      │
│  - licenses.yaml.enc (加密)     │
└────────────┬────────────────────┘
             │
             │ GitHub Actions
             │ 自动构建 Docker 镜像
             ↓
┌─────────────────────────────────┐
│  Docker Hub (Public)            │
│  xiangyugongzuoliu/             │
│  chuangcut-video-workflow:latest│
│  - 预置 LICENSE_ENCRYPTION_KEY  │
│  - 预置 licenses.yaml.enc       │
└────────────┬────────────────────┘
             │
             │ 镜像引用
             ↓
┌─────────────────────────────────┐
│  公开模板仓库 (Public)           │
│  chuangcut-video-workflow-      │
│  template                       │
│  - template.yaml                │
│  - README.md                    │
│  - 部署文档                      │
└────────────┬────────────────────┘
             │
             │ Zeabur CLI 提交
             ↓
┌─────────────────────────────────┐
│  Zeabur 平台                    │
│  用户一键部署                    │
│  - 输入 LICENSE_KEY             │
│  - 自动拉取镜像                  │
│  - 配置环境变量                  │
└─────────────────────────────────┘
```

---

## ✅ 验证清单

部署前检查：

- [ ] template.yaml 中 `expose: true` 已添加到 LICENSE_KEY 和 ENCRYPTION_KEY
- [ ] README.md 中部署按钮包含 `?referralCode=xiangyugongzuoliu`
- [ ] Docker 镜像 `xiangyugongzuoliu/chuangcut-video-workflow:latest` 已发布且支持多平台
- [ ] template.yaml 使用 `PREBUILT` 模板类型
- [ ] 健康检查路径 `/api/health` 在应用中已实现
- [ ] 环境变量默认值合理
- [ ] 资源限制配置合理（推荐 4 CPU / 8GB RAM）
- [ ] volumes 配置包含所有必需目录（/data, /temp, /output, /logs）

---

## 🔐 授权系统配置

### GitHub Secrets 配置

在私有仓库 `chuangcut-video-workflow` 的 Settings → Secrets → Actions 中配置：

| Secret 名称 | 说明 | 示例 |
|------------|------|------|
| `DOCKER_USERNAME` | Docker Hub 用户名 | `xiangyugongzuoliu` |
| `DOCKER_PASSWORD` | Docker Hub 访问令牌 | `dckr_pat_xxxxx...` |
| `LICENSE_ENCRYPTION_KEY` | 授权加密密钥 | `06109778438b336b9fd8289bcba3bdf2853b9c67c7024cd4944d9d0cf22977ac` |

### 授权文件管理

1. **生成授权码**：

```bash
cd /path/to/chuangcut-video-workflow

# 生成新授权码
pnpm license:generate --customer="客户名称" --days=365

# 输出会包含 YAML 配置，添加到 config/licenses.yaml
```

2. **加密授权文件**：

```bash
# 设置加密密钥（与 GitHub Secret 一致）
export LICENSE_ENCRYPTION_KEY=06109778438b336b9fd8289bcba3bdf2853b9c67c7024cd4944d9d0cf22977ac

# 执行加密
pnpm license:encrypt

# 输出: config/licenses.yaml.enc
```

3. **提交并构建镜像**：

```bash
git add config/licenses.yaml.enc
git commit -m "更新授权配置"
git push origin main

# GitHub Actions 自动构建新镜像
```

---

## 🆘 常见问题

### Q: 为什么需要公开仓库？
A: Zeabur 要求模板引用的 Git 仓库必须公开，但我们通过公开配置仓库 + 私有源码的方式保护了代码。

### Q: 如何更新已部署的服务？
A: 更新私有仓库代码 → GitHub Actions 自动构建新镜像 → Zeabur 自动拉取 → 用户手动重启服务。

### Q: 模板更新后现有部署会受影响吗？
A: 不会。模板更新只影响新的部署，已部署的服务需要手动重新部署才能应用新配置。

### Q: 如何删除模板？
A: `zeabur template delete -c <template-code>`（不影响已部署的服务）

### Q: 用户如何获取授权码？
A: 供应商生成授权码后，通过邮件、微信等方式提供给客户。授权码格式：`CCUT-XXXXXX-XXXXXXXXXXXX-XXXX`

### Q: LICENSE_ENCRYPTION_KEY 会泄露吗？
A: 不会。该密钥通过 GitHub Secrets 注入到镜像构建过程，最终只在镜像内部使用，用户不可见。

### Q: 如何撤销某个授权码？
A: 修改 `config/licenses.yaml` 中对应授权的 `status` 为 `revoked`，重新加密并推送镜像即可。

---

## 📚 相关文档

- [Zeabur 官方文档](https://zeabur.com/docs)
- [Zeabur CLI 文档](https://zeabur.com/docs/deploy/cli)
- [Docker Buildx 文档](https://docs.docker.com/buildx/working-with-buildx/)
- [GitHub Actions 文档](https://docs.github.com/en/actions)

---

## 📞 联系方式

- **维护者**: 翔宇工作流
- **邮箱**: support@example.com
- **最后更新**: 2025-11-19

---

**注意**：本文档仅供供应商内部使用，请勿公开分享。
