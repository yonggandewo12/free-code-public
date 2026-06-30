# free-code

<p align="center">
  <img src="assets/screenshot.png" alt="free-code" width="720" />
</p>

<p align="center">
  <strong>Claude Code 的自由构建版。</strong><br>
  去除了遥测，上游限制更少，并开放更多实验性功能。<br>
  本项目通过当前仓库源码进行构建与运行。
</p>

> **源码来源说明**
>
> 当前仓库整理与修改基于以下来源：
>
> 1. [paoloanzn/free-code](https://github.com/paoloanzn/free-code)
> 2. Anthropic Claude Code 上游源码/能力体系
>
> 当前仓库是在上述来源基础上继续进行本地化修改、功能扩展与文档整理的版本；具体功能和使用方式以**当前仓库实现**为准。

---

## 目录

- [项目简介](#项目简介)
- [环境要求](#环境要求)
- [从当前仓库安装与启动](#从当前仓库安装与启动)
- [一键安装脚本（可选）](#一键安装脚本可选)
- [通过 GitHub Releases 下载预构建二进制](#通过-github-releases-下载预构建二进制)
- [配置模型与密钥](#配置模型与密钥)
- [Provider Profiles（多厂商配置）](#provider-profiles多厂商配置)
- [OpenAI GPT 专项说明](#openai-gpt-专项说明)
- [常用启动方式](#常用启动方式)
- [内置角色（12 个）](#内置角色12-个)
- [支持的提供方](#支持的提供方)
- [构建变体](#构建变体)
- [实验性功能](#实验性功能)
- [SQLite 持久化记忆系统](#sqlite-持久化记忆系统)
- [项目结构](#项目结构)
- [技术栈](#技术栈)
- [IPFS 镜像](#ipfs-镜像)
- [贡献](#贡献)
- [许可证](#许可证)

---

## 项目简介

这是一个可构建的 Claude Code 源码分支版本，适合在本地终端中作为 AI 编码 CLI 使用。

相对于上游快照，本项目主要做了三类调整：

### 1. 去除遥测

- 去除了 OpenTelemetry / gRPC / Sentry / 自定义事件上报等遥测链路
- GrowthBook 仍可在本地用于功能开关判断，但不会向外回传统计
- 不再默认进行崩溃上报、使用统计或会话指纹回传

### 2. 去除额外提示层限制

- 去除了 CLI 包裹在模型外层的一些额外系统提示限制
- 模型自身的原生安全能力仍然存在

### 3. 开放更多实验功能

- 本仓库支持构建带实验特性的版本
- 推荐使用 `bun run build:dev:full` 构建完整开发版

---

## 环境要求

- **操作系统**：macOS / Linux / Windows PowerShell / Windows Git Bash / WSL
- **运行时**：`Bun >= 1.3.11`
- **其他工具**：`git`
- **模型接入**：至少准备一种可用方式
  - Anthropic API Key
  - 自定义兼容接口 + API Key
  - 或对应提供方的 OAuth / 云凭证

如果尚未安装 Bun：

```bash
curl -fsSL https://bun.sh/install | bash
```

---

## 从当前仓库安装与启动

> 这是本 README 推荐的主流程：**就在你当前已经拿到的仓库目录中，直接安装、构建、启动。**

### 1）进入当前仓库目录

如果你现在已经位于本仓库根目录，可以跳过这一步；否则先进入当前仓库：

```bash
cd free-code
```

### 2）安装依赖

```bash
bun install
```

### 3）构建项目

推荐构建命令：

```bash
bun run build:dev:full
```

构建完成后会生成：

- `./cli-dev`

如果你只想构建标准版：

```bash
bun run build
```

会生成：

- `./cli`

### 4）启动交互式 CLI

如果你使用推荐构建：

```bash
./cli-dev
```

如果你构建的是标准版：

```bash
./cli
```

### 5）源码模式启动（可选）

如果你不想先编译，也可以直接从源码启动：

```bash
bun run dev
```

说明：

- 启动速度会比编译产物稍慢
- 更适合调试、排查问题、临时运行

### 6）推荐的最短路径

```bash
cd free-code
bun install
bun run build:dev:full
./cli-dev
```

---

## 一键安装脚本（可选）

如果你已经拿到了当前仓库源码，可以直接执行本地安装脚本。

### macOS / Linux / Git Bash

```bash
bash ./install.sh
```

### Windows PowerShell

```powershell
.\install.ps1
```

脚本行为：

1. 检查并创建默认配置文件（仅在缺失时创建，不覆盖已有文件）：
   - `~/.claude/settings.json`
   - `~/.claude.json`
2. 检查 `git`，如果未安装则直接退出
3. 检查 `bun`，如果未安装则自动安装
4. 默认执行 `bun run build:dev:full`
5. 安装命令入口到用户目录，使你可以在任意位置运行 `freecode` / `free-code` / `claudecode`
6. 支持增量执行：
   - 依赖没变化时跳过 `bun install`
   - 源码没变化时跳过 `build:dev:full`
   - `--force` / `-Force` 可强制完整执行

### 强制全量重新安装

```bash
bash ./install.sh --force
```

```powershell
.\install.ps1 -Force
```

### 安装完成后运行

```bash
freecode
```

或：

```bash
free-code
```

或：

```bash
claudecode
```

脚本会在仓库根目录生成 `.install-state.json`，用于记录依赖和构建指纹，以便后续重复执行时加速。

默认配置模板维护在源码目录中：

- `assets/default-settings.template.json`
- `assets/default-claude.template.json`

其中 `settings.json` 模板内置了真实使用示例：火山引擎、MiniMax、OpenCode Zen。

如果你要修改源码、调试问题或自行维护构建，依然推荐优先使用当前仓库目录直接执行脚本，而不是依赖远端安装命令。

### 卸载

#### macOS / Linux / Git Bash

```bash
bash ./uninstall.sh
```

或：

```bash
bash ./install.sh --uninstall
```

#### Windows PowerShell

```powershell
.\uninstall.ps1
```

或：

```powershell
.\install.ps1 -Uninstall
```

卸载会移除：
- 命令入口（`freecode` / `free-code` / `claudecode`）
- 安装脚本写入的 PATH 条目

卸载不会移除：
- `~/.claude/settings.json`
- `~/.claude.json`
- 源码目录与构建产物

#### 从 Release 直接卸载

如果你是通过远程安装脚本安装的，也可以直接远程执行卸载：

macOS / Linux：

```bash
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/install.sh | bash -s -- --uninstall
```

Windows PowerShell：

```powershell
iex "& { $(irm https://github.com/yonggandewo12/free-code-public/releases/latest/download/install.ps1) } -Uninstall"
```

---

## 通过 npm 安装（推荐）

如果你已经安装了 Node.js（>=18），可以通过 npm 直接安装：

```bash
npm i -g myfreecode
```

npm 会自动根据你的平台下载对应的预构建二进制。

> **注意**：Windows 用户目前建议通过源码安装（`install.ps1`），因为 Bun compile 在 Windows 上的 standalone executable 存在已知崩溃问题（[Bun issue](https://github.com/oven-sh/bun/issues)）。npm 安装方式在 Windows 上可能无法正常工作。

安装完成后直接运行：

```bash
freecode
# 或
free-code
# 或
claude
```

### 更新

```bash
npm i -g myfreecode@latest
```

### 卸载

```bash
npm uninstall -g myfreecode
```

---

## 通过 GitHub Releases 下载预构建二进制

每次推送 `v*` tag 或推送到 `main` 分支时，GitHub Actions 会自动构建 5 个平台的预编译二进制，并发布到 GitHub Releases。

### 下载

前往 [Releases 页面](https://github.com/yonggandewo12/free-code-public/releases)，下载对应平台的二进制文件：

| 平台 | 架构 | 文件名 |
|------|------|--------|
| macOS | x64 | `cli-darwin-x64` |
| macOS | ARM64 (Apple Silicon) | `cli-darwin-arm64` |
| Linux | x64 | `cli-linux-x64` |
| Linux | ARM64 | `cli-linux-arm64` |
| Windows | x64 | `cli-win32-x64.exe` |

### 安装

**一键安装（推荐，自动检测平台）：**

```bash
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/install.sh | bash
```

> 从源码目录安装时也可以使用 `bash ./install.sh --release` 下载预构建二进制，无需 bun。

**手动下载：**

```bash
# macOS ARM64 (Apple Silicon)
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/cli-darwin-arm64 -o /usr/local/bin/freecode

# macOS x64
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/cli-darwin-x64 -o /usr/local/bin/freecode

# Linux x64
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/cli-linux-x64 -o /usr/local/bin/freecode

# Linux ARM64
curl -fsSL https://github.com/yonggandewo12/free-code-public/releases/latest/download/cli-linux-arm64 -o /usr/local/bin/freecode

# 添加执行权限
chmod +x /usr/local/bin/freecode

# 创建别名命令（free-code / claudecode）
ln -sf /usr/local/bin/freecode /usr/local/bin/free-code
ln -sf /usr/local/bin/freecode /usr/local/bin/claudecode
```

**Windows（PowerShell）：**

```powershell
# Windows x64
Invoke-WebRequest -Uri https://github.com/yonggandewo12/free-code-public/releases/latest/download/cli-win32-x64.exe -OutFile freecode.exe

# 创建别名命令（free-code / claudecode）
'@echo off
"%~dp0freecode.exe" %*' | Set-Content -Encoding ascii free-code.cmd
'@echo off
"%~dp0freecode.exe" %*' | Set-Content -Encoding ascii claudecode.cmd
'& "$PSScriptRoot/freecode.exe" $args' | Set-Content -Encoding ascii free-code.ps1
'& "$PSScriptRoot/freecode.exe" $args' | Set-Content -Encoding ascii claudecode.ps1
```

安装完成后运行：

```bash
freecode
```

安装完成后也可使用：

```bash
free-code
```

```bash
claudecode
```

### 与源码安装的区别

| | 源码安装（`install.sh`） | GitHub Releases 下载 |
|---|---|---|
| 需要 Bun | 是 | 否 |
| 需要编译 | 是 | 否，预编译二进制 |
| 需要 GitHub 访问 | 是（仓库需可访问） | 是（Releases 页面） |
| 更新方式 | `git pull` + 重新构建 | 重新下载最新 Release |

---

## 配置模型与密钥

### 方式一：环境变量（推荐）

#### Anthropic 直连

```bash
export ANTHROPIC_API_KEY="你的 API Key"
./cli-dev
```

#### 自定义 API / 中转 / 兼容接口

```bash
export ANTHROPIC_API_KEY="你的 API Key"
export ANTHROPIC_BASE_URL="https://your-provider.example/v1"
./cli-dev --model your-model-id
```

> 如果你使用自定义源，建议同时显式指定 `--model`。

#### OpenAI GPT

```bash
export CLAUDE_CODE_USE_OPENAI=1
./cli-dev
```

#### AWS Bedrock

```bash
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION="us-east-1"
./cli-dev
```

#### Google Vertex AI

```bash
export CLAUDE_CODE_USE_VERTEX=1
./cli-dev
```

#### Anthropic Foundry

```bash
export CLAUDE_CODE_USE_FOUNDRY=1
export ANTHROPIC_FOUNDRY_API_KEY="你的 API Key"
./cli-dev
```

### 方式二：交互页面登录

启动 CLI 后，在交互页面输入：

```
/login
```

适用于支持 OAuth 的接入方式。

对于 OpenAI GPT，本仓库当前推荐使用：

```text
/gpt-login
```

登录成功后会自动：

- 保存 GPT OAuth 凭证
- 将当前 provider 切换到 OpenAI
- 让 `/model` 可直接看到 GPT 模型

### 方式三：写入 shell 配置文件

如果你不想每次都手动导出环境变量，可以写入 shell 配置文件：

```bash
# ~/.zshrc 或 ~/.bashrc
export ANTHROPIC_API_KEY="你的 API Key"
export ANTHROPIC_BASE_URL="https://your-provider.example/v1"
```

然后执行：

```bash
source ~/.zshrc
```

### 方式四：使用 settings.json 定义多厂商 profile（推荐）

如果你希望：

- 在配置文件中定义多个模型厂商 / 网关
- 为每个厂商定义自己的默认模型和可选模型
- 在 CLI 内部交互式切换厂商
- 输入一次 API Key 后长期复用

那么推荐使用下方的 **Provider Profiles** 方案。

---

## Provider Profiles（多厂商配置）

本项目现在支持在 `settings.json` 中定义多个 **provider profile**，每个 profile 可以包含：

- `provider`：厂商类型（`firstParty` / `bedrock` / `vertex` / `foundry` / `openai` / `openaiChat`）
- `apiFormat`：API 格式（`anthropic` / `openai`），默认 `anthropic`。当设为 `openai` 时，请求会自动从 Anthropic Messages 格式翻译为 OpenAI Chat Completions 格式
- `baseUrl`：该厂商 / 网关的 API 地址
- `model`：该 profile 默认模型
- `availableModels`：该 profile 下允许选择的模型
- `customModels`：给 CLI 展示的自定义模型目录
- `env`：附加环境变量
- `apiKeyEnv`：该 profile 使用哪个 API key 环境变量

### 配置文件位置

可放在以下任一位置：

- `~/.claude/settings.json`
- `<项目目录>/.claude/settings.json`
- `<项目目录>/.claude/settings.local.json`

### 推荐配置模板

> **注意**：最外层的 `model` 必须有，且后面的 `model` 必须有且只有一个匹配最外层的 `model`，否则会触发走官方的通道校验。

```json
{
  "model": "MiniMax-M2.7",
  "providerProfiles": [
    {
      "id": "openrouter-sonnet",
      "name": "OpenRouter Sonnet",
      "provider": "firstParty",
      "baseUrl": "https://openrouter.example/api/v1",
      "model": "router-sonnet",
      "availableModels": [
        "router/sonnet-latest",
        "router/opus-latest"
      ],
      "customModels": [
        {
          "id": "router-sonnet",
          "model": "router/sonnet-latest",
          "name": "Router Sonnet",
          "description": "日常开发默认模型"
        },
        {
          "id": "router-opus",
          "model": "router/opus-latest",
          "name": "Router Opus",
          "description": "复杂任务使用"
        }
      ]
    },
    {
      "id": "azure-foundry",
      "name": "Azure Foundry",
      "provider": "foundry",
      "model": "azure-sonnet",
      "apiKeyEnv": "ANTHROPIC_FOUNDRY_API_KEY",
      "customModels": [
        {
          "id": "azure-sonnet",
          "model": "my-azure-sonnet-deployment",
          "name": "Azure Sonnet",
          "description": "Azure Foundry 部署模型"
        }
      ],
      "env": {
        "ANTHROPIC_FOUNDRY_BASE_URL": "https://your-resource.services.ai.azure.com"
      }
    },
    {
      "id": "bedrock-prod",
      "name": "AWS Bedrock",
      "provider": "bedrock",
      "model": "claude-sonnet-4-6"
    },
    {
      "id": "deepseek",
      "name": "DeepSeek",
      "provider": "openaiChat",
      "baseUrl": "https://api.deepseek.com/v1",
      "model": "deepseek-chat",
      "availableModels": [
        "deepseek-chat",
        "deepseek-reasoner"
      ],
      "customModels": [
        {
          "id": "deepseek-chat",
          "model": "deepseek-chat",
          "name": "DeepSeek V3",
          "description": "DeepSeek 通用对话模型"
        },
        {
          "id": "deepseek-reasoner",
          "model": "deepseek-reasoner",
          "name": "DeepSeek R1",
          "description": "DeepSeek 推理模型"
        }
      ],
      "apiKeyEnv": "DEEPSEEK_API_KEY"
    }
  ]
}
```

编辑或新增 `.claude.json` 文件，修改或新增 `hasCompletedOnboarding` 字段值为 `true`：

```json
{
  "hasCompletedOnboarding": true
}
```

### 真实使用示例：火山引擎 + MiniMax + OpenCode Zen

如果你希望同时接入：

- 火山引擎：`https://ark.cn-beijing.volces.com/api/coding`（Anthropic 格式）
- MiniMax：`https://api.minimaxi.com/anthropic`（Anthropic 格式）
- OpenCode Zen：`https://opencode.ai/zen/v1`（OpenAI Chat Completions 格式，免费模型）

可以直接参考下面这份配置。

#### 1. settings.json

> **配置文件路径**：
> - macOS / Linux：`~/.claude/settings.json`
> - Windows：`C:\Users\<用户名>\.claude\settings.json`

```json
{
  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Read(*)",
      "Glob(*)",
      "Grep(*)",
      "Agent(Explore)",
      "Agent(code-reviewer)",
      "Agent(security-reviewer)"
    ],
    "defaultMode": "acceptEdits"
  },
  "skipWebFetchPreflight": true,
  "model": "minimax-m2.5-free",
  "providerProfiles": [
    {
      "id": "volcengine-doubao",
      "name": "火山引擎 Doubao",
      "provider": "firstParty",
      "baseUrl": "https://ark.cn-beijing.volces.com/api/coding",
      "model": "doubao-seed-2.0-pro-260215",
      "availableModels": [
        "doubao-seed-2.0-pro-260215",
        "kimi-k2.5"
      ],
      "customModels": [
        {
          "id": "doubao-seed-2.0-pro-260215",
          "model": "doubao-seed-2.0-pro-260215",
          "name": "Doubao Seed 2.0 Pro",
          "description": "火山引擎最新编码模型"
        },
        {
          "id": "kimi-k2.5",
          "model": "kimi-k2.5",
          "name": "Kimi K2.5",
          "description": "Kimi K2.5 编码模型"
        }
      ],
      "apiKeyEnv": "ANTHROPIC_API_KEY"
    },
    {
      "id": "minimax",
      "name": "MiniMax",
      "provider": "firstParty",
      "baseUrl": "https://api.minimaxi.com/anthropic",
      "model": "MiniMax-M2.7",
      "availableModels": [
        "MiniMax-M2.7",
        "MiniMax-M2.5-highspeed"
      ],
      "customModels": [
        {
          "id": "MiniMax-M2.7",
          "model": "MiniMax-M2.7",
          "name": "MiniMax-M2.7",
          "description": "MiniMax 最新模型"
        },
        {
          "id": "MiniMax-M2.5-highspeed",
          "model": "MiniMax-M2.5-highspeed",
          "name": "MiniMax-M2.5-highspeed",
          "description": "MiniMax-M2.5-highspeed"
        }
      ],
      "apiKeyEnv": "ANTHROPIC_API_KEY"
    },
    {
      "id": "opencode-zen",
      "name": "OpenCode Zen (免费模型)",
      "provider": "openaiChat",
      "baseUrl": "https://opencode.ai/zen/v1",
      "model": "big-pickle",
      "availableModels": [
        "big-pickle",
        "minimax-m2.5-free",
        "ling-2.6-flash",
        "hy3-preview-free",
        "nemotron-3-super-free"
      ],
      "customModels": [
        {
          "id": "big-pickle",
          "model": "big-pickle",
          "name": "Big Pickle",
          "description": "OpenCode Zen 免费编码模型"
        },
        {
          "id": "minimax-m2.5-free",
          "model": "minimax-m2.5-free",
          "name": "MiniMax M2.5 Free",
          "description": "MiniMax M2.5 免费版"
        },
        {
          "id": "ling-2.6-flash",
          "model": "ling-2.6-flash",
          "name": "Ling 2.6 Flash",
          "description": "Ling 2.6 快速模型"
        },
        {
          "id": "hy3-preview-free",
          "model": "hy3-preview-free",
          "name": "Hy3 Preview Free",
          "description": "Hy3 预览免费版"
        },
        {
          "id": "nemotron-3-super-free",
          "model": "nemotron-3-super-free",
          "name": "Nemotron 3 Super Free",
          "description": "Nemotron 3 Super 免费版"
        }
      ],
      "apiKeyEnv": "OPENCODE_ZEN_API_KEY"
    }
  ]
}
```

#### 2. .claude.json

> **配置文件路径**：
> - macOS / Linux：`~/.claude.json`
> - Windows：`C:\Users\<用户名>\.claude.json`

```json
{
  "hasCompletedOnboarding": true
}
```

### 交互式管理：`/profiles` 命令

除了手动编辑 `settings.json`，你还可以在 CLI 中使用 `/profiles`（别名 `/profile`）以交互式界面管理 provider profiles：

```
/profiles
```

支持以下操作：

- **列表查看**：展示所有已配置的 profile
- **新增 profile**：逐步引导填写 provider 类型、ID、名称、Base URL、API Key 环境变量、默认模型
- **编辑 profile**：修改已有 profile 的任意字段
- **删除 profile**：删除 profile（级联删除关联的 custom models）
- **管理模型**：进入 profile 的模型管理界面，支持新增/编辑/删除 custom models

> **注意**：`/profiles` 修改的配置写入 `~/.claude/settings.json`，与手动编辑完全等效。

### 使用方式

1. 启动 CLI
2. 输入 `/provider` 选择厂商（也可以直接 `/provider <profile-id>`）
3. 首次选择该厂商时输入对应 API Key
4. API Key 会被缓存，后续再次切换到该 profile 时可直接复用
5. 输入 `/model` 切换当前厂商下的模型

例如：

```
/provider
/model kimi
/model qwen
```

说明：

- `providerProfiles[].id` 用于切换厂商 profile
- `customModels[].id` 用于切换模型
- `customModels[].model` 是实际发送给厂商的真实模型 ID
- 重新输入 API Key 会覆盖之前缓存的值

### 切换方式

启动 CLI 后执行：

```
/provider
```

作用：

- 切换当前使用的厂商 profile
- 自动应用该 profile 的 `provider` / `baseUrl` / `env`
- 自动切换该 profile 的默认模型
- 对需要 API Key 的 profile，提示你输入 key

### API Key 持久化行为

对需要 API Key 的 profile（例如自定义网关、Foundry 等）：

- 第一次选中时，会提示输入 API Key
- 输入一次后会**持久保存**并在后续继续使用
- 下次再次切换到该 profile 时，默认复用已保存的 key
- 如果重新输入新的 key，则会**覆盖旧 key**
- 如果输入框留空并直接确认，则继续使用已保存的 key

### 模型切换方式

选中 provider profile 后，继续使用：

```
/model
```

或直接：

```
/model router-opus
```

这里的 `router-opus` 是你在 `customModels[].id` 中定义的模型选择名。

CLI 实际发送给 provider 的会是对应的真实模型 ID，例如：

- `router-opus` -> `router/opus-latest`

### 配置建议

1. `availableModels` 建议写 **真实模型 ID**，最稳妥
2. `customModels[].id` 建议写成便于记忆的短名称，方便 `/model` 切换
3. 每个 profile 最好显式配置 `model`
4. 如果某个 profile 使用特殊 key 名，显式设置 `apiKeyEnv`

### OpenAI Chat Completions 格式支持

本项目支持接入任何兼容 OpenAI Chat Completions API（`/v1/chat/completions`）的端点，内部会自动将 Anthropic Messages 格式翻译为 OpenAI Chat Completions 格式，并将流式响应翻译回来。

> 说明：默认 `assets/default-settings.template.json` 只保留火山引擎、MiniMax、OpenCode Zen 三组示例；OpenRouter 等路由平台请按下面方式手动新增 profile。

#### 从旧版 OpenRouter 模板迁移（可选）

如果你以前直接使用过内置 `openrouter-qwen` 示例，可按下面迁移：

1. 保留你现有的 OpenRouter profile（不需要删除）。
2. 确认该 profile 含 `apiFormat: "openai"`（或将 `provider` 改为 `openaiChat`）。
3. 将 `apiKeyEnv` 设为你实际使用的环境变量（如 `OPENROUTER_API_KEY`）。
4. 在 `~/.claude/settings.json` 中确认 `model` 与 `availableModels/customModels` 对齐。
5. 运行 `/provider <你的 profile id>` 后再用 `/model <模型别名>` 验证切换。

#### 仅 OpenCode Zen 快速接入（3 步）

1. 设置环境变量（macOS / Linux）：

```bash
export OPENCODE_ZEN_API_KEY="你的 Key"
```

2. 在 `~/.claude/settings.json` 增加一个最小 profile：

```json
{
  "id": "opencode-zen",
  "name": "OpenCode Zen",
  "provider": "openaiChat",
  "baseUrl": "https://opencode.ai/zen/v1",
  "model": "big-pickle",
  "apiKeyEnv": "OPENCODE_ZEN_API_KEY"
}
```

3. 启动后执行：

```text
/provider opencode-zen
/model big-pickle
```

> **关于免费模型限流的说明**
>
> OpenCode Zen 的免费模型（如 `deepseek-v4-flash-free`）有每日使用额度限制。当额度用尽时，
> 会返回 `429 FreeUsageLimitError`，`retry-after` 通常约 17 小时。
> 这是 Zen 后端基于账号/IP 的限流，与客户端无关。
>
> 本项目已自动注入与官方 opencode CLI 一致的请求头（`User-Agent`、`x-opencode-session` 等），
> 确保请求被识别为官方客户端流量，享受正常的限流策略。

#### 扩展用 OpenAI 格式 profile（不写入默认模板）

以下 profile 仅作为扩展示例，按需手动加入 `~/.claude/settings.json` 的 `providerProfiles`；
默认 `assets/default-settings.template.json` 不会内置这些项。

```json
[
  { "id": "openrouter", "name": "OpenRouter", "provider": "openaiChat", "baseUrl": "https://openrouter.ai/api/v1", "model": "openai/gpt-4o-mini", "apiKeyEnv": "OPENROUTER_API_KEY" },
  { "id": "deepseek", "name": "DeepSeek", "provider": "openaiChat", "baseUrl": "https://api.deepseek.com/v1", "model": "deepseek-chat", "apiKeyEnv": "DEEPSEEK_API_KEY" },
  { "id": "together", "name": "Together", "provider": "openaiChat", "baseUrl": "https://api.together.xyz/v1", "model": "meta-llama/Llama-3.1-8B-Instruct-Turbo", "apiKeyEnv": "TOGETHER_API_KEY" },
  { "id": "fireworks", "name": "Fireworks", "provider": "openaiChat", "baseUrl": "https://api.fireworks.ai/inference/v1", "model": "accounts/fireworks/models/llama-v3p1-8b-instruct", "apiKeyEnv": "FIREWORKS_API_KEY" },
  { "id": "groq", "name": "Groq", "provider": "openaiChat", "baseUrl": "https://api.groq.com/openai/v1", "model": "llama-3.1-8b-instant", "apiKeyEnv": "GROQ_API_KEY" },
  { "id": "dashscope-openai", "name": "DashScope OpenAI", "provider": "openaiChat", "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1", "model": "qwen-plus", "apiKeyEnv": "DASHSCOPE_API_KEY" },
  { "id": "siliconflow", "name": "SiliconFlow", "provider": "openaiChat", "baseUrl": "https://api.siliconflow.cn/v1", "model": "Qwen/Qwen2.5-7B-Instruct", "apiKeyEnv": "SILICONFLOW_API_KEY" },
  { "id": "zhipu-openai", "name": "Zhipu OpenAI", "provider": "openaiChat", "baseUrl": "https://open.bigmodel.cn/api/paas/v4", "model": "glm-4-flash", "apiKeyEnv": "ZHIPU_API_KEY" },
  { "id": "xai", "name": "xAI", "provider": "openaiChat", "baseUrl": "https://api.x.ai/v1", "model": "grok-3-mini", "apiKeyEnv": "XAI_API_KEY" },
  { "id": "mistral-openai", "name": "Mistral OpenAI", "provider": "openaiChat", "baseUrl": "https://api.mistral.ai/v1", "model": "mistral-small-latest", "apiKeyEnv": "MISTRAL_API_KEY" },
  { "id": "modal-glm", "name": "Modal GLM-5.1", "provider": "openaiChat", "baseUrl": "https://api.us-west-2.modal.direct/v1", "model": "zai-org/GLM-5.1-FP8", "apiKeyEnv": "MODAL_API_KEY" }
]
```

`availableModels` 与 `customModels` 都是可选项；先用最小配置跑通，再按需补充即可。

适用场景：

- OpenRouter、Together、Fireworks 等模型路由平台
- DeepSeek、Qwen、GLM 等国产模型的官方 API
- vLLM、Ollama、LocalAI 等本地部署的模型服务
- OpenCode Zen 等免费模型平台

#### 两种配置方式

**方式一：`apiFormat: "openai"`（推荐）**

适用于 `firstParty` 或其他 provider 类型，但端点使用 OpenAI Chat Completions 格式的情况：

```json
{
  "id": "my-openai-gateway",
  "name": "My OpenAI Gateway",
  "provider": "firstParty",
  "apiFormat": "openai",
  "baseUrl": "https://your-gateway.example/v1",
  "model": "your-model-id",
  "apiKeyEnv": "MY_GATEWAY_API_KEY"
}
```

**方式二：`provider: "openaiChat"`（简写）**

等同于 `provider: "firstParty"` + `apiFormat: "openai"`，更简洁：

```json
{
  "id": "deepseek",
  "name": "DeepSeek",
  "provider": "openaiChat",
  "baseUrl": "https://api.deepseek.com/v1",
  "model": "deepseek-chat",
  "apiKeyEnv": "DEEPSEEK_API_KEY"
}
```

#### `openai` vs `openaiChat` 区别

| | `openai`（内置 OpenAI GPT） | `openaiChat`（通用 OpenAI Chat 兼容） |
|---|---|---|
| 目标 API | OpenAI Responses API (`/responses`) | OpenAI Chat Completions API (`/chat/completions`) |
| 认证方式 | ChatGPT OAuth 登录（`/gpt-login`） | 标准 API Key（`Authorization: Bearer <key>`） |
| 触发方式 | `CLAUDE_CODE_USE_OPENAI=1` 或 `/provider openai` | `provider: "openaiChat"` 或 `apiFormat: "openai"` |
| 适用范围 | 仅 ChatGPT 官方 Codex 后端 | 任何 OpenAI Chat Completions 兼容端点 |
| 格式翻译 | Anthropic Messages ↔ OpenAI Responses | Anthropic Messages ↔ OpenAI Chat Completions |

> **简单判断**：如果端点地址包含 `/v1/chat/completions` 或自称"兼容 OpenAI 格式"，就用 `openaiChat` 或 `apiFormat: "openai"`。

#### 格式翻译细节

当 `apiFormat: "openai"` 或 `provider: "openaiChat"` 时，以下翻译自动发生：

| Anthropic 格式 | OpenAI Chat Completions 格式 |
|---|---|
| `system` 数组 | `system` 角色消息 |
| `messages` (user/assistant) | `messages` (user/assistant) |
| `tool_use` 内容块 | `tool_calls` 数组 |
| `tool_result` 内容块 | `tool` 角色消息 |
| `input_schema` | `parameters` |
| `thinking` 块 | 自动忽略（非标准字段） |
| `cache_control` | 自动忽略 |
| `betas` | 自动忽略 |

#### 无缝切换回 Anthropic 模型

当你从 OpenAI Chat 兼容的 provider 切换回 Anthropic 模型时，格式翻译自动停止，请求直接发送到 Anthropic API：

```
/provider opencode-zen    → 使用 OpenAI Chat 格式翻译
/provider minimax         → 使用 Anthropic 格式直连
/provider openai          → 使用 OpenAI Responses 格式（GPT OAuth）
```

---

## OpenAI GPT 专项说明

相对于此前版本/旧分支，本仓库当前关于 OpenAI 接入的行为已经补齐为：

1. **新增 GPT 专用浏览器登录入口**
   - 支持 `/gpt-login`
   - `/login` 中也提供 `OpenAI GPT account` 选项

2. **GPT 登录成功后自动切换到 OpenAI provider**
   - 不再只保存 token 而不切 provider
   - 登录后 `/model` 会直接显示 GPT 模型

3. **`/provider` 现在内建 OpenAI GPT 入口**
   - 可直接使用 `/provider`
   - 也可直接使用 `/provider openai`
   - 不要求你先手写一个 OpenAI provider profile

4. **切走再切回 OpenAI 时默认复用 GPT 授权**
   - 例如先切到 MiniMax，再切回 OpenAI
   - 一般不需要重新 `/gpt-login`

5. **状态显示更清楚**
   - `/status` 会显示当前 provider、OpenAI auth 状态、auth health、`Runtime model`
   - 顶部 Logo 区域也会提示当前 provider / GPT auth saved

6. **模型切换反馈更直接**
   - `/model gpt-5.4` / `/model gpt-5.2` 成功后，会直接提示当前 runtime 已切到哪一个模型、通过哪个 provider 路由生效

7. **OpenAI 路由切换后的引导更完整**
   - 如果已经有 GPT 登录，`/provider openai` 会直接切到内建 OpenAI GPT 路由
   - 如果还没有 GPT 登录，会明确提示先 `/gpt-login`，再 `/model gpt-5.4`

---

## OpenAI GPT 详细操作流程

### 场景一：首次使用 GPT 模型

1. 启动 CLI：

```bash
./cli-dev
```

2. 执行登录：

```text
/gpt-login
```

3. 浏览器完成授权后，回到 CLI。

4. 查看当前 provider / 状态：

```text
/status
```

此时应能看到：

- API provider: `OpenAI GPT`
- OpenAI auth: 已授权
- Runtime model: `GPT 5.4` 或 `GPT 5.2`（取决于当前实际运行模型）

5. 选择 GPT 模型：

```text
/model
```

当前内建可见模型为：

- `gpt-5.4`
- `gpt-5.2`

例如：

```text
/model gpt-5.4
```

设置成功后，CLI 会直接回显类似：

```text
Set model to GPT 5.4 · Runtime now uses GPT 5.4 via OpenAI GPT
```

### 场景二：直接切到 OpenAI

如果你已经登录过 GPT，希望以后直接切回 OpenAI：

> **内置快捷示例**：`/provider openai`
>
> 这是当前版本内置推荐的最快切换方式。
> - 如果已经登录过 GPT，会直接切到 OpenAI GPT 路由并复用已保存的登录状态
> - 如果还没有 GPT 登录，会先弹出确认，并引导你立即开始 GPT 浏览器登录流程
> - 如果当前还不是 GPT runtime model，会继续提示你执行 `/model gpt-5.4`

```text
/provider openai
```

或：

```text
/provider
```

然后选择：

```text
OpenAI GPT
```

如果已有 GPT 授权，CLI 会直接复用，不需要重新登录。

如果还没有 GPT 授权，CLI 会先显示一个确认框：

- `OpenAI sign-in required`
- `Sign in now`
- `Cancel`

选择 `Sign in now` 后，会直接进入 GPT 登录流程；登录成功后再完成 OpenAI provider 切换。

如果你取消登录，当前版本也会明确提示下一步：

- `Run /gpt-login, then /model gpt-5.4 to finish switching to a GPT runtime model.`

### 场景三：OpenAI 与 MiniMax 来回切换

假设你已经：

- 完成 `/gpt-login`
- 也在 `settings.json` 中配置了 `minimax` profile

#### 1）先切到 OpenAI GPT

```text
/provider openai
/model gpt-5.4
```

#### 2）切到 MiniMax

```text
/provider minimax
/model MiniMax-M2.7
```

#### 3）再切回 OpenAI

```text
/provider openai
/model
```

此时通常：

- **不需要重新 `/gpt-login`**
- GPT 授权仍会保留
- `/model` 中会再次出现 GPT 模型

### 场景四：怎么看是否需要重新登录

执行：

```text
/status
```

关注以下字段：

- `OpenAI auth`
- `OpenAI auth status`
- `Runtime model`

例如，当你已经执行：

```text
/provider openai
/model gpt-5.2
```

此时 `/status` 应该能帮助你确认两件事：

- 当前 provider 已经是 `OpenAI GPT`
- 当前实际运行模型已经是 `GPT 5.2`

### 场景五：排查“看起来切了 provider，但模型没切”的情况

建议按下面顺序确认：

```text
/provider openai
/model gpt-5.4
/status
```

预期结果：

- `/provider openai` 成功后，会提示当前 provider 已切到 `OpenAI GPT`
- `/model gpt-5.4` 成功后，会提示 `Runtime now uses GPT 5.4 via OpenAI GPT`
- `/status` 中会看到：
  - `API provider: OpenAI GPT`
  - `Runtime model: GPT 5.4`

如果你看到 provider 已切换，但 `Runtime model` 仍不是 GPT 系列，请优先重新执行：

```text
/model gpt-5.4
```

再重新检查 `/status`。

可能出现的典型状态：

- `Signed in via /gpt-login`
- `Expired locally · will refresh automatically on next use`
- `Not signed in · run /gpt-login`
- `Expired · sign in again with /gpt-login`

含义：

- **Signed in**：当前可直接使用
- **Expired locally · will refresh automatically on next use**：本地看已过期，但下次实际请求时会尝试 refresh，一般不用手动重登
- **Not signed in / Expired · sign in again with /gpt-login**：需要重新登录

## 常用启动方式

### 交互模式

```bash
# 从源码构建
./cli-dev

# 或从 GitHub Releases 下载的二进制
freecode
```

```bash
free-code
```

```bash
claudecode
```

### 单次执行

```bash
./cli-dev -p "列出当前目录下的文件"
```

### 指定模型

```bash
./cli-dev --model claude-sonnet-4-6
```

### 从源码启动

```bash
bun run dev
```

### 常见的交互内命令

- `/help`：查看帮助
- `/login`：登录
- `/config`：查看配置
- `/config-agents`：配置内置 Agent 的模型、启用/禁用、Provider Profile
- `/provider`：切换厂商 profile 或内建 OpenAI GPT，并可输入/覆盖该 profile 的 API Key
- `/model`：切换模型
- `/goal`：自然语言设定目标，自动规划并执行 workflow（`/goal list` 查看，`/goal resume <name>` 续做）
- `/workflows`：浏览并选择 workflow 执行

---

## 系统内置 Agent（6 个）

系统级 Agent，由代码内置定义，可通过 `/config-agents` 命令配置模型和启用状态。

| Agent | 说明 | 默认模型 | 默认启用 |
|-------|------|---------|---------|
| **Explore** | 快速搜索文件和代码模式（只读） | haiku | ✅ |
| **Plan** | 探索代码库，设计分步实现方案（只读） | inherit | ✅ |
| **Claude Code Guide** | 解答 Claude Code CLI / Agent SDK / API 使用问题 | haiku | ✅ |
| **Verification** | 构建、测试、lint 验证 + 对抗性探测 | inherit | ❌ |
| **General Purpose (Worker)** | 复杂多步骤任务，工具不受限 | inherit | ✅ |
| **Code Reviewer** | 结构化代码审查（正确性/安全性/健壮性/测试覆盖） | inherit | ❌ |

### 模型选择建议

- **Explore / Claude Code Guide**：轻量查询任务，`haiku` 足够；复杂代码库分析可升级 `sonnet`
- **Plan / Verification / Code Reviewer**：需要深度推理，建议 `sonnet` 或 `opus`；简单任务 `haiku` 即可
- **General Purpose**：根据任务复杂度选择，`inherit`（继承父会话模型）是合理默认值
- 所有 Agent 均支持 `inherit`（继承父会话模型）和通过 Provider Profile 使用非 Claude 模型

### 配置方式

1. 交互式：运行 `/config-agents`，选择 Agent → 修改模型 / 启用禁用 / 切换 Provider
2. 手动：编辑 `~/.claude/settings.json` 中的 `agentConfigs` 字段：

```json
{
  "agentConfigs": {
    "Plan": { "model": "opus" },
    "code-reviewer": { "enabled": true, "model": "sonnet" },
    "verification": { "enabled": true }
  }
}
```

---

## 自定义角色（12 个）

当前仓库内置 12 个中文专家角色（自定义 Agent），角色定义文件位于：

```text
agents/
```

这些角色可用于多代理协作、任务拆解、设计评审、实现审查、QA 与上线前核查等场景。

### 角色列表

1. `UI设计师`
   - 专注视觉设计系统、组件库与像素级界面创建

2. `产品经理`
   - 负责需求发现、战略规划、路线图与跨团队协同

3. `代码审查员`
   - 聚焦正确性、可维护性、安全性与性能的代码审查

4. `前端开发专家`
   - 专注现代 Web 技术、UI 实现与前端性能优化

5. `后端架构师`
   - 专注可扩展系统设计、数据库架构、API 与云基础设施

6. `多代理编排器`
   - 负责组织完整开发流程，协调多个专家角色推进交付

7. `安全工程师`
   - 专注威胁建模、漏洞评估、安全代码审查与安全架构

8. `现实核查员`
   - 基于证据进行最终集成验证与上线准备评估

9. `证据收集员`
   - 以截图和可见证据为核心的 QA 角色

10. `软件架构师`
    - 专注系统设计、领域驱动设计、架构模式与技术决策

11. `高级开发工程师`
    - 擅长复杂实现与高质量落地的高级全栈开发角色

12. `高级项目经理`
    - 擅长把规格和需求拆解成清晰、可执行、可验证任务

### 说明

- 这些角色的详细说明写在各自的 `agents/*.md` 文件中
- 如需在 Claude 本地角色目录中使用这些角色，需要将对应角色文件复制到用户目录下的 `agents` 文件夹：
  - macOS / Linux：`~/.claude/agents/`
  - Windows：`C:\Users\<用户名>\.claude\agents\`
- 也就是说，可将本仓库中的角色文件复制到如下目标目录：
  - macOS / Linux：`~/.claude/agents/xxx`
  - Windows：`C:\Users\<用户名>\.claude\agents\`
- 适合用于需求分析、架构设计、实现分工、代码评审、测试验收和上线核查
- 如果你在仓库中继续扩展角色体系，建议同步更新本节文档

---

## 支持的提供方

本项目支持以下接入方式：

| 提供方 | 启用方式 | 认证方式 |
|---|---|---|
| Anthropic（默认） | 无需额外开关 | `ANTHROPIC_API_KEY` 或 OAuth |
| OpenAI GPT | `CLAUDE_CODE_USE_OPENAI=1` 或 `/provider openai` | `/gpt-login` OAuth |
| OpenAI Chat 兼容 | `provider: "openaiChat"` 或 `apiFormat: "openai"` | API Key |
| AWS Bedrock | `CLAUDE_CODE_USE_BEDROCK=1` | AWS 凭证 |
| Google Vertex AI | `CLAUDE_CODE_USE_VERTEX=1` | `gcloud` ADC |
| Anthropic Foundry | `CLAUDE_CODE_USE_FOUNDRY=1` | `ANTHROPIC_FOUNDRY_API_KEY` |

### Anthropic 默认模型

| 模型 | ID |
|---|---|
| Claude Opus 4.6 | `claude-opus-4-6` |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` |
| Claude Haiku 4.5 | `claude-haiku-4-5` |

### 常用环境变量

| 变量名 | 作用 |
|---|---|
| `ANTHROPIC_API_KEY` | Anthropic API Key |
| `ANTHROPIC_AUTH_TOKEN` | 可选认证 token |
| `ANTHROPIC_BASE_URL` | 自定义 API 地址 |
| `ANTHROPIC_MODEL` | 默认模型 |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | 自定义 Opus 模型 ID |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | 自定义 Sonnet 模型 ID |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | 自定义 Haiku 模型 ID |
| `CLAUDE_CODE_OAUTH_TOKEN` | 通过环境变量提供 OAuth token |
| `CLAUDE_CODE_USE_OPENAI` | 启用 OpenAI 提供方 |
| `CLAUDE_CODE_USE_BEDROCK` | 启用 AWS Bedrock |
| `CLAUDE_CODE_USE_VERTEX` | 启用 Vertex AI |
| `CLAUDE_CODE_USE_FOUNDRY` | 启用 Foundry |

---

## 构建变体

| 命令 | 输出 | 说明 |
|---|---|---|
| `bun run build` | `./cli` | 标准构建 |
| `bun run build:dev` | `./cli-dev` | 开发构建 |
| `bun run build:dev:full` | `./cli-dev` | 开发构建 + 当前仓库可用的完整实验特性 |
| `bun run compile` | `./dist/cli` | 输出到 `dist/` 的构建版本 |
| `bun run build:all` | `./dist/cli-*` | 构建所有平台的预构建二进制（5 个平台） |

### 跨平台构建

`build.ts` 支持 `--target` 参数指定交叉编译目标：

```bash
# 构建特定平台
bun run ./scripts/build.ts --compile --target bun-linux-x64
bun run ./scripts/build.ts --compile --target bun-darwin-arm64
bun run ./scripts/build.ts --compile --target bun-windows-x64
```

可选目标：

| 目标 | 输出文件 |
|------|---------|
| `bun-darwin-x64` | `dist/cli-darwin-x64` |
| `bun-darwin-arm64` | `dist/cli-darwin-arm64` |
| `bun-linux-x64` | `dist/cli-linux-x64` |
| `bun-linux-arm64` | `dist/cli-linux-arm64` |
| `bun-windows-x64` | `dist/cli-win32-x64.exe` |

### GitHub Actions 自动构建与发布

推送代码到 `main` 分支或推送 `v*` tag 即可自动触发构建：

- **推送 main**：自动构建所有平台二进制，结果可在 Actions 页面下载
- **推送 tag**（如 `v2.1.88`）：构建并创建 GitHub Release，附带所有平台二进制

```bash
# 升级版本号
# 编辑 package.json 中的 version 字段

# 提交并打 tag
git add .
git commit -m "release v2.1.88"
git tag v2.1.88
git push origin main --tags
```

### 自定义开启特性

```bash
# 只开启指定功能
bun run ./scripts/build.ts --feature=ULTRAPLAN --feature=ULTRATHINK

# 在 dev 构建上额外开启某功能
bun run ./scripts/build.ts --dev --feature=BRIDGE_MODE
```

---

## SQLite 持久化记忆系统

本项目内置了基于 SQLite + FTS5 的持久化记忆系统，替代传统的 Markdown 文件记忆方式。

### 核心特性

- **SQLite + FTS5 全文搜索**：支持数千条记忆的高效检索，不浪费 context window
- **中文分词**：jieba-wasm 应用层分词 + FTS5 unicode61，精确匹配中文内容
- **双层作用域**：`global`（user/feedback，跨项目共享）和 `project`（project/reference，项目隔离）
- **自动驱逐**：超过 5000 条或 50MB 时自动淘汰低价值记忆，保持记忆库精炼
- **自动迁移**：首次启动时自动从 Markdown 文件记忆迁移到数据库
- **损坏恢复**：启动时 `PRAGMA integrity_check` 检测损坏，自动备份并重建

### 检索流程

```
用户输入 → jieba 分词 → FTS5 搜索（四层回退）→ 加权排序 → 注入 system-reminder
```

**四层回退策略**：

| 优先级 | 策略 | 说明 |
|--------|------|------|
| 1 | AND 查询 | 精确匹配所有关键词 |
| 1.5 | 逐步去掉最短 token 的 AND | AND 无结果时，按长度丢弃最短 token 重试 |
| 2 | OR 查询 | 任一关键词匹配 |
| 3 | LIKE 模糊搜索 | FTS5 完全无结果时兜底 |

**加权排序公式**（替代 LLM 精排，省去调用成本）：

```
score = BM25 × 0.7 + recency × -0.2 + access_freq × -0.1
```

升序排序（越小越靠前），高频访问和最近访问的记忆优先。

### 数据存储

- 数据库路径：`~/.claude/memory.db`
- 唯一键：`(scope, project_hash, name)`
- `project_hash`：项目路径 SHA256 前 16 字符，用于项目级记忆隔离

### 记忆类型

| 类型 | 作用域 | 说明 |
|------|--------|------|
| `user` | global | 用户角色、偏好、专长 |
| `feedback` | global | 工作方式指导、纠正与确认 |
| `project` | project | 项目工作上下文、决策、截止日期 |
| `reference` | project | 外部系统指针和资源 |

### 使用方式

记忆由 LLM 通过 `memory_write` 工具自动写入，通过 FTS5 检索自动注入为 `<system-reminder>`。用户也可通过 `/memory` 命令手动管理：

| 命令 | 功能 |
|------|------|
| `/memory` | 显示帮助 + 统计概览 |
| `/memory search <query>` | FTS5 搜索 |
| `/memory list [type]` | 列出记忆（可按类型筛选） |
| `/memory project [limit]` | 列出当前项目记忆 |
| `/memory get <name>` | 查看指定记忆 |
| `/memory stats` | 记忆总数、类型分布、数据库大小 |
| `/memory dedup` | 查找冗余记忆（`--confirm` 执行删除） |
| `/memory delete <name>` | 删除指定记忆（需 `--confirm`） |
| `/memory evict` | 手动触发驱逐（需 `--confirm`） |

### Feature Flag

通过编译时 `SQLITE_MEMORY` flag 门控，默认构建中包含。关闭时回退到传统 Markdown 文件记忆系统。

---

推荐使用：

```bash
bun run build:dev:full
```

该命令会构建带完整实验功能集合的 `./cli-dev`。

更多特性清单可参考：[FEATURES.md](FEATURES.md)

### Workflow Scripts

`WORKFLOW_SCRIPTS` 是本仓库新增的 workflow 子系统，用来把可复用的工作流定义成 markdown 文件，并通过命令或工具入口执行为后台任务。

它支持：

- 从 `.claude/workflows/` 和内置 workflow 定义中发现工作流
- 通过 `/workflows` 浏览并选择 workflow
- 通过 `/goal` 用自然语言描述目标，自动规划并生成 workflow 文件并执行
- 通过 `Workflow` 工具由模型触发后台执行
- 在后台任务面板里查看步骤进度、输出和错误
- 每个 workflow step 会以可见 child agent 的形式运行，能在详情页查看状态、token 和工具使用情况
- 支持在 workflow frontmatter 中声明多个 agent，按 agent team 的方式执行同一 workflow step

#### Goal 命令（`/goal`）

用自然语言描述目标，自动调用 Plan agent 分析代码库、设计方案，生成标准 7 步 workflow 文件并执行：

```text
/goal 给博客系统添加草稿功能
```

**标准 7 步结构：**

| Step | 任务 | Agent | 说明 |
|------|------|-------|------|
| 1 | 编码实现（含构建自检） | general-purpose | 并行编码，**自行运行构建确保通过，失败继续修复** |
| 2 | 代码审查 | code-reviewer | 审查正确性/安全性/健壮性，输出 APPROVE/REQUEST_CHANGES |
| 3 | 修复 CR 问题（含构建自检） | general-purpose | 修复审查问题，修复完自运行构建 |
| 4 | 最终代码审查 | code-reviewer | 确认所有问题已关闭 |
| 5 | 修复最终问题（含构建自检） | general-purpose | 修复遗漏项，修复完自运行构建 |
| 6 | 最终构建验证（验证关卡） | verification | 独立运行构建。**FAIL → 回退到 step 5 修复 → 重试 1 次，仍 FAIL → workflow 终止** |
| 7 | 结果汇总 | general-purpose | 变更摘要 + 步骤状态 + 使用示例 |

**验证失败自动回退**：Step 6 的 verification agent 独立运行构建验证，如果返回 FAIL，workflow 自动回到 step 5 让 general-purpose 修复代码，然后重新执行 step 6。重试 1 次仍失败则 workflow 终止。

**增量续做**：Workflow 失败后可通过 `/goal resume <name>` 从失败步骤继续，已完成的步骤不会重复执行。

**构建系统自动检测**：Verification agent 支持 12 种构建系统自动检测：

| 构建系统 | 检测文件 | 命令 |
|---------|---------|------|
| Bun | bun.lock | `bun run build` |
| Node.js | package.json | `npm/yarn/pnpm build` |
| Python | pyproject.toml | `python -m build` |
| Java/Maven | pom.xml | `mvn verify` |
| Java/Gradle | build.gradle | `./gradlew build` |
| Go | go.mod | `go build ./...` |
| Rust | Cargo.toml | `cargo build` |
| C/C++ | CMakeLists.txt | `cmake --build build` |
| .NET | *.csproj | `dotnet build` |
| Ruby | Gemfile | `bundle exec rake` |
| Zig | build.zig | `zig build` |
| Makefile（独立） | Makefile | `make` |

**管理命令**：

| 命令 | 作用 |
|------|------|
| `/goal list` | 列出所有 workflow 及其状态（✅已完成/🔄进行中/❌异常/⏸️未开始） |
| `/goal resume <name>` | 从失败步骤续做 workflow |
| `/workflows` | 浏览并手动选择 workflow 执行 |

#### onFail 配置（Workflow 作者用）

在 workflow step 中可通过 `onFail` 行配置验证失败时的自动回退行为：

```
### 6. 最终构建验证
agents: [verification]
onFail: gotoStep=5, maxRetries=1
```

- `gotoStep`：失败后回退到哪个步骤（1-indexed）
- `maxRetries`：最大重试次数（>= 1）

引擎会在验证步骤返回 FAIL 时，自动清空回退范围内步骤的摘要，重置状态为 pending，跳转到 `gotoStep` 重新执行。

#### 一个完整的 Goal 生成的 workflow 示例

```md
---
name: add-draft-feature
description: 为博客系统添加草稿保存和编辑功能
context: fork
agents: [general-purpose, verification, code-reviewer]
when_to_use: 当需要为博客添加草稿功能时使用
---

## Goal
为博客系统添加草稿功能，支持保存草稿、编辑草稿和发布。

## Steps

### 1. 编码实现（含构建自检）
agents: [general-purpose]

- Sub-module 1: 数据模型（Draft 类型 + 数据库迁移）
- Sub-module 2: API 路由（CRUD /api/drafts）

**硬性要求**：完成编码后必须自行运行项目构建命令确保编译通过。

**Success criteria**
- Draft 数据模型和迁移创建完成
- API 路由实现完整
- 构建 exit code 0（自检通过）

### 2. 代码审查
agents: [code-reviewer]

审查全部变更文件的正确性、安全性、健壮性和代码风格。

**Success criteria**
- 审查所有变更文件，列出所有问题
- 每个发现必须有文件:行号和证据支持

### 3. 修复审查问题（含构建自检）
agents: [general-purpose]

修复 step 2 中发现的所有问题。修复完成后自行运行构建。

**Success criteria**
- 问题全部修复
- 构建 exit code 0（自检通过）

### 4. 最终代码审查
agents: [code-reviewer]

确认全部问题已关闭，代码满足合并标准。

**Success criteria**
- 所有之前发现的问题已修复
- 给出最终 APPROVE 结论

### 5. 修复最终问题（含构建自检）
agents: [general-purpose]

修复 step 4 中发现的遗漏项。修复完成后自行运行构建。

**Success criteria**
- 遗漏项全部修复
- 构建 exit code 0（自检通过）

### 6. 最终构建验证（验证关卡）
agents: [verification]
onFail: gotoStep=5, maxRetries=1

独立运行项目构建命令验证编译通过，输出 PASS/FAIL 结论。

**Success criteria**
- 构建 exit code 0，无编译错误

### 7. 结果汇总
agents: [general-purpose]

汇总执行结果。

**Success criteria**
- 变更文件摘要
- 各步骤状态
- 使用示例
```

放置位置：

- `.claude/workflows/*.md`

使用建议：

1. 优先使用 `./cli-dev` 或开启 `WORKFLOW_SCRIPTS` 的构建。
2. 通过 `/goal <描述>` 让系统自动生成并执行 workflow（推荐）。
3. 自定义 workflow 直接放入 `.claude/workflows/`。
4. 通过 `/workflows` 选择 workflow，或者让模型在合适场景下调用 `Workflow` 工具。
5. 如果需要多 agent 协作，可以用 `agents:` frontmatter 声明 agent team。
6. 验证型 step 使用 `agents: [verification]` 并配置 `onFail` 实现自动回退修复。

---

## 项目结构

| 路径 | 说明 |
|---|---|
| `src/entrypoints/cli.tsx` | CLI 入口 |
| `src/screens/REPL.tsx` | 主交互界面 |
| `src/commands.ts` | 命令注册 |
| `src/tools.ts` | 工具注册 |
| `src/commands/` | 各类命令实现 |
| `src/tools/` | 各类工具实现 |
| `src/commands/workflows/` | workflow 命令入口 |
| `src/tools/WorkflowTool/` | workflow 工具、解析与权限流 |
| `src/services/` | API / OAuth / MCP / 分析等服务 |
| `src/components/` | 终端 UI 组件 |
| `src/hooks/` | React hooks |
| `src/tasks/` | 后台任务系统 |
| `src/tasks/LocalWorkflowTask/` | workflow 后台运行时与持久化 |
| `src/memdb/` | SQLite + FTS5 持久化记忆系统 |
| `.claude/workflows/` | 项目级 workflow 定义目录 |
| `scripts/build.ts` | 构建脚本 |
| `scripts/build-all.ts` | 跨平台全量构建脚本 |

---

## 技术栈

- **Runtime / Bundler**：Bun
- **CLI UI**：React + Ink
- **语言**：TypeScript
- **云接入**：Anthropic / OpenAI / Bedrock / Vertex / Foundry

---

## IPFS 镜像

如果你在网络环境受限的情况下使用本项目，可根据仓库后续说明选择镜像分发方式。

---

## 贡献

欢迎提交 Issue 和 PR。

建议在提交前至少完成：

```bash
bun run build:dev:full
```

确保当前仓库可以正常构建。

---

## 许可证

请以仓库中的实际许可证文件为准。
