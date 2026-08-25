<p align="center">
  <img src="assets/logo.png" alt="Awesome DSH Plugins" width="160">
</p>

<h1 align="center">Awesome DSH Plugins</h1>

<p align="center">
  精选的 <a href="https://github.com/deepseek-ai/deepseek-harness">DeepSeek Harness</a> / DSH 插件、工具和扩展。
</p>

<p align="center">
  <a href="README.md">English</a>
</p>

本清单按**本地 dsh 实际安装的插件**编写（`~/.dsh/profiles/web`，即 `dsh web` profile）。
每条记录都注明本地安装版本与观察到的插件间依赖关系。

## 目录

- [已安装插件](#已安装插件)
- [Image Generation 图片生成](#image-generation-图片生成)
- [Cost & Usage 用量与费用](#cost--usage-用量与费用)
- [Workflow 工作流](#workflow-工作流)
- [Compatibility 兼容层](#compatibility-兼容层)
- [插件依赖关系](#插件依赖关系)
- [安装](#安装)
- [许可证](#许可证)

## 已安装插件

当前安装在本地 `web` profile（`~/.dsh/profiles/web/package.json`）的插件：

| 插件 | 版本 | 类型 | 安装来源 |
| --- | --- | --- | --- |
| `@LiuRJ99/dsh-cpa-plugin` | 0.3.0 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) |
| `@LiuRJ99/dsh-workbuddy-provider` | 0.2.0 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) |
| `dsh-better-sidebar` | 0.16.0 | Web UI | [github.com/omdsh-dev/DSH-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) |
| `dsh-image-gen` | 0.2.0 | 图片生成 | [github.com/LiuRJ99/dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen)（[shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的 fork） |
| `dsh-sandbox-schema-shim` | 0.1.1 | 兼容层 | [github.com/xiaohj233/dsh-compat-shims](https://github.com/xiaohj233/dsh-compat-shims) |
| `dsh-spend` | 0.5.0 | 用量与费用 | [github.com/nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) |
| `dsh-taskboard` | 0.5.1 | 工作流 / 任务看板 | [github.com/LiuRJ99/dsh-taskboard-cloader](https://github.com/LiuRJ99/dsh-taskboard-cloader)（[cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的 fork） |

## Image Generation 图片生成

- [dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) —— 图片生成（Gemini、OpenAI、Seedream、DashScope 等）· **已安装 v0.2.0** ✅ —— [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的个人 fork（CPA 适配版），**需要 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0**（见[插件依赖关系](#插件依赖关系)）。本地配置：`image-generation.engine: gemini`，图片保存到 `dsh-image-gen/` 工作区目录。
- `@LiuRJ99/dsh-cpa-plugin`（`image-generation` 子路径）—— CPA 供应商同时暴露支持生图的模型（`gemini-3.1-flash-image`、`gpt-image-1.5`、`gpt-image-2`）· **已安装 v0.3.0** ✅

## Cost & Usage 用量与费用

- [dsh-spend](https://github.com/nonewind/dsh-spend) —— token 调用量、多维度统计、自动识别计费计划（Code/Token）与费用估算 · **已安装 v0.5.0** ✅

## Workflow 工作流

- [dsh-taskboard](https://github.com/LiuRJ99/dsh-taskboard-cloader) —— agent 优先的任务看板：host 侧任务账本 + `taskboard_*` agent 工具、项目认领边界、按任务指定模型执行、cron 定时、git worktree 隔离、SSE 实时看板 · **已安装 v0.5.1** ✅ —— [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的个人 fork，带本地改动（本地源码目录 `dsh-taskboard-cloader`）

## Compatibility 兼容层

- [dsh-sandbox-schema-shim](https://github.com/xiaohj233/dsh-compat-shims) —— 在 `danger-full-access` 会话中移除模型侧 shell / file 工具 schema 里冗余的沙箱提权字段 · **已安装 v0.1.1** ✅（`dsh-compat-shims` monorepo 中的 `dsh-sandbox-schema-shim` 包）

## 插件依赖关系

根据已安装包的 `dependencies` / `peerDependencies` 与源码 import 观察：

```text
dsh-image-gen 0.2.0 ──(硬依赖)──▶ @LiuRJ99/dsh-cpa-plugin ≥0.3.0 <0.4.0
                                  (import 了 '@LiuRJ99/dsh-cpa-plugin/image-generation')

@LiuRJ99/dsh-cpa-plugin ──────────▶ @deepseek-ai/dsh-llm、dsh-credentials、dsh-settings
                                    (把 'CLIProxyAPI' 供应商注册进 llm-pi-ai)

@LiuRJ99/dsh-workbuddy-provider ──▶ @deepseek-ai/dsh-llm、dsh-credentials、dsh-settings
                                    (把 'WorkBuddy' 供应商注册进 llm-pi-ai，本地端口 8318)

dsh-better-sidebar ──────────────── 服务提供方：向其他插件开放 ctx.betterSidebar
                                    (registerTab / registerFileViewer)；
                                    可选集成，不存在反向硬依赖

dsh-taskboard ───────────────────── 零 peer 依赖；自包含
dsh-spend ───────────────────────── 只依赖官方 @deepseek-ai/dsh-home-paths、
                                    dsh-typert-protocol、schemastery
dsh-sandbox-schema-shim ─────────── 独立兼容层，无第三方依赖
```

要点：

1. **`dsh-image-gen` 0.2.0 硬依赖 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0** —— 需先装 CPA 插件，否则图片生成无法解析依赖。
2. **`dsh-cpa-plugin` 与 `dsh-workbuddy-provider` 是平行的模型供应商插件**，都注册进官方 `llm-pi-ai` 供应商注册表（`CLIProxyAPI` 端口 8317、`WorkBuddy` 端口 8318），二者互不依赖。
3. **`dsh-better-sidebar` 是可选的服务提供方** —— 其他插件可通过 `ctx.betterSidebar` 注册侧边栏页签 / 文件预览器；当前已装插件均不强制依赖它。
4. **`dsh-taskboard`、`dsh-spend`、`dsh-sandbox-schema-shim` 自包含** —— 无插件间依赖。

## 安装

```bash
# 向 web profile 添加插件
dsh plugin --profile web add <包名或-github-引用>

# 示例：从 npm 安装（预构建产物）
dsh plugin --profile web add dsh-taskboard

# 示例：从 GitHub monorepo 子路径安装
dsh plugin --profile web add "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
```

安装后重启 `dsh web` 并刷新页面。

## 许可证

本清单本身为 MIT。各插件保留各自许可证
（`dsh-taskboard` 为 Apache-2.0，其余为 MIT）。
