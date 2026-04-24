---
name: codex-image
description: 基于 Codex auth.json (ChatGPT OAuth) 认证，通过 Responses API + image_generation 工具生成图片。当用户要求生成图片、画图、AI 作画时使用。
---

# Codex Image Skill

基于 Codex 本地 `~/.codex/auth.json` 认证凭证，调用 ChatGPT 后端 Responses API 生成图片。

## 使用方式

```bash
python scripts/codex_image.py generate "你的图片描述"
```

### 常用参数

- `--size <WxH>` — 图片尺寸，如 `1024x1024`、`1792x1024`
- `--quality <low|medium|high>` — 图片质量
- `--out <path>` — 指定输出路径
- `--name <prefix>` — 输出文件名前缀
- `--dry-run` — 预览请求体，不实际调用 API

### 示例

```bash
# 基础出图
python scripts/codex_image.py generate "一只可爱的猫坐在书堆上，吉卜力风格"

# 指定尺寸和质量
python scripts/codex_image.py generate --size 1792x1024 --quality high "赛博朋克城市夜景"

# 指定输出路径
python scripts/codex_image.py generate --out ./output/cover.png "科技感封面图"
```

## 认证机制

从 `~/.codex/auth.json` 读取 ChatGPT OAuth 的 `access_token`，自动检测过期并使用 `refresh_token` 刷新。

**认证优先级：**
1. 环境变量 `OPENAI_API_KEY`
2. `auth.json` 中的 `OPENAI_API_KEY` 字段
3. `auth.json` 中的 ChatGPT OAuth `tokens.access_token`

## 技术要点

- **API 端点：** `https://chatgpt.com/backend-api/codex/responses`
- **出图方式：** Responses API + `image_generation` 工具
- **响应格式：** SSE 流式响应，从 `image_generation_call` 事件提取 base64 图片
- **默认模型：** `gpt-5.5`（可通过 `--model` 覆盖）
- **依赖：** 仅 Python 3.11+ 标准库，无第三方依赖
