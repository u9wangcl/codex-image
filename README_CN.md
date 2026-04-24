# codex-image

[English](./README.md)

> **Agent Skill** — 本项目是一个 Agent Skill，既可独立使用，也可集成到 AI 编码助手（Codex CLI、Claude Code、Gemini CLI 等）中，为其提供图片生成能力。将本项目放到工程的 `.skills/` 目录下，Agent 会自动发现并在需要生成图片时调用。

使用本地 Codex CLI 登录凭证生成图片 —— 无需配置 API Key。

本工具读取 `codex login` 生成的 `~/.codex/auth.json`，通过 ChatGPT 后端 Responses API 的内置 `image_generation` 工具生成图片。

## 特性

- 🔑 **零配置** — 复用已有的 Codex CLI 登录状态
- 🔄 **自动续期** — 检测 JWT 过期并自动刷新 token
- 🖼️ **高质量出图** — 基于 GPT 图像生成能力
- 📦 **零依赖** — 仅使用 Python 3.11+ 标准库
- 🖥️ **跨平台** — Windows、macOS、Linux

## 前置条件

1. **Python 3.11+**
2. **Codex CLI** 已登录 — 如未登录，请先运行 `codex login`

确认认证文件存在：

```bash
cat ~/.codex/auth.json
```

## 快速开始

```bash
# 克隆
git clone https://github.com/u9wangcl/codex-image.git
cd codex-image

# 生成图片
python scripts/codex_image.py "一只可爱的猫坐在书堆上，吉卜力风格"
```

## 使用方法

```bash
python scripts/codex_image.py [选项] "图片描述"
```

### 参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--model` | 模型名称 | `gpt-5.5` |
| `--size` | 图片尺寸（如 `1024x1024`、`1792x1024`） | `1024x1024` |
| `--quality` | `low` / `medium` / `high` | `medium` |
| `--format` | `png` / `jpeg` / `webp` | `png` |
| `--out` | 输出文件路径 | 自动生成 |
| `--name` | 输出文件名前缀 | `generated` |
| `--dry-run` | 仅预览请求体，不调用 API | — |

### 示例

```bash
# 基础出图
python scripts/codex_image.py "赛博朋克城市夜景，霓虹灯"

# 自定义尺寸和质量
python scripts/codex_image.py --size 1792x1024 --quality high "日落时分的山间风景"

# 指定输出路径
python scripts/codex_image.py --out ./output/cover.png "科技感封面图"

# 预览模式
python scripts/codex_image.py --dry-run "测试 prompt"
```

### 平台入口脚本

```bash
# Linux / macOS
bash scripts/generate.sh "图片描述"

# Windows
scripts\generate.cmd "图片描述"
```

## 工作原理

```
~/.codex/auth.json
        │
        ▼
  ┌─────────────┐     POST (SSE 流式)        ┌──────────────────────────────────────┐
  │ 加载 OAuth   │ ──────────────────────────▶ │ chatgpt.com/backend-api/codex/       │
  │ access_token │                             │              responses               │
  └─────────────┘     ◀─────────────────────── │  + image_generation 工具              │
        │              image_generation_call    └──────────────────────────────────────┘
        ▼                   (base64)
  ┌─────────────┐
  │ 保存为 PNG   │
  └─────────────┘
```

### 认证优先级

1. 环境变量 `OPENAI_API_KEY`（最高优先级）
2. `auth.json` 中的 `OPENAI_API_KEY` 字段
3. `auth.json` 中的 ChatGPT OAuth `tokens.access_token`

## 项目结构

```
codex-image/
├── README.md           # 英文文档
├── README_CN.md        # 中文文档
├── SKILL.md            # Agent Skill 定义
└── scripts/
    ├── codex_image.py  # 核心脚本（~250 行）
    ├── generate.sh     # Linux/macOS 入口
    └── generate.cmd    # Windows 入口
```

## 环境变量

| 变量名 | 说明 |
|--------|------|
| `OPENAI_API_KEY` | 使用 API Key 覆盖 OAuth 认证 |
| `OPENAI_BASE_URL` | 自定义 API 端点 |
| `CODEX_HOME` | 自定义 Codex 主目录（默认 `~/.codex`） |
| `CODEX_AUTH_FILE` | 自定义认证文件路径 |
| `CODEX_IMAGE_OUTPUT_DIR` | 自定义输出目录 |

## 许可证

MIT

