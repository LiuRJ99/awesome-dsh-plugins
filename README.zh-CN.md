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
- [Desktop Automation 桌面控制](#desktop-automation-桌面控制)
- [Browser Control 浏览器控制](#browser-control-浏览器控制)
- [Model Providers 模型供应商](#model-providers-模型供应商)
- [Image Generation 图片生成](#image-generation-图片生成)
- [Cost & Usage 用量与费用](#cost--usage-用量与费用)
- [Workflow 工作流](#workflow-工作流)
- [Web UI 界面增强](#web-ui-界面增强)
- [Compatibility 兼容层](#compatibility-兼容层)
- [插件依赖关系](#插件依赖关系)
- [安装](#安装)
- [许可证](#许可证)

## 已安装插件

当前安装在本地 `web` profile（`~/.dsh/profiles/web/package.json`）的插件：

| 插件 | 版本 | 类型 | 安装来源 |
| --- | --- | --- | --- |
| `@LiuRJ99/dsh-cpa-plugin` | 0.4.0 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) |
| `@LiuRJ99/dsh-workbuddy-provider` | 0.2.0 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) |
| `@yuxianglin/dsh-bridge-browser` | 0.0.4 | 浏览器控制 | [github.com/LiuRJ99/dsh-browser](https://github.com/LiuRJ99/dsh-browser)（[Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) 的 fork；workspace/扩展 v0.1.3） |
| `@zibokapi/dsh-codex-computer-use` | 0.1.2 | 桌面自动化 / 电脑控制 | [github.com/geohotstan/dsh-computer-use](https://github.com/geohotstan/dsh-computer-use) |
| `dsh-better-sidebar` | 0.16.1 | Web UI | [github.com/omdsh-dev/DSH-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) |
| `dsh-image-gen` | 0.3.0 | 图片生成 | [github.com/LiuRJ99/dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen)（[shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的 fork） |
| `dsh-sandbox-schema-shim` | 0.1.1 | 兼容层 | [github.com/xiaohj233/dsh-compat-shims](https://github.com/xiaohj233/dsh-compat-shims) |
| `dsh-spend` | 0.6.0 | 用量与费用 | [github.com/nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) |
| `dsh-taskboard` | 0.6.0 | 工作流 / 任务看板 | [github.com/LiuRJ99/dsh-taskboard-cloader](https://github.com/LiuRJ99/dsh-taskboard-cloader)（[cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的 fork） |

## Desktop Automation 桌面控制

- [@zibokapi/dsh-codex-computer-use](https://github.com/geohotstan/dsh-computer-use) —— 专为 macOS 打造的桌面 Computer Use（对标 OpenAI Codex Computer Use）：常驻 Swift 辅助守护进程（`dsh-computer-daemon.app`）、无障碍树（Accessibility Tree）窗口捕获（`computer_use_get_app_state`）、屏幕截图、基于 SkyLight（`SLEventPostToPid`）与 `CGEvent.postToPid` 回退的后台鼠标/键盘输入模拟（无需切到前台/不抢焦点）、应用级审批门禁（`ctx.computer`）、`computer-use` skill 以及独立的 MCP stdio 服务（`dsh-computer-mcp`）· **已安装 v0.1.2** ✅（本地源码目录 `dsh-computer-use`）

## Browser Control 浏览器控制

- [dsh-browser](https://github.com/LiuRJ99/dsh-browser) —— 让 DSH 连接你正在使用的 Chrome 或 Firefox 标签页：`@yuxianglin/dsh-bridge-browser` 桥接插件（token 认证的 WebSocket 通道 + 纯文本 `browser_*` 工具 —— `browser_snapshot` / `browser_click` / `browser_type` / `browser_navigate` / `browser_screenshot` / `browser_network_capture` 等）+ Chrome/Firefox MV3 侧边栏扩展，保留登录态、会话与 cookie · **桥接已安装 v0.0.4，workspace/扩展 v0.1.3** ✅ —— [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) 的个人 fork，新增 Firefox MV3 支持（本地源码目录 `dsh-browser`）

## Model Providers 模型供应商

- [@LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) —— 支持 Web GUI 动态配置的 CLIProxyAPI Responses 供应商，提供多账号管理、额度窗口解析（Codex 5 小时与周额度窗口合并计算）、模型响应速度偏好扩展以及 `./image-generation` 生图服务子路径 · **已安装 v0.4.0** ✅（本地源码目录 `dsh-cpa-plugin`）
- [@LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) —— 无缝接入本地 WorkBuddy 内置模型（本地端口 8318），注册进官方 `llm-pi-ai` 模型注册表 · **已安装 v0.2.0** ✅（本地源码目录 `dsh-workbuddy-provider`）

## Image Generation 图片生成

- [dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) —— 图片生成（GPT Image / Gemini Image）· **已安装 v0.3.0** ✅ —— [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的个人 fork（CPA 适配版），**需要 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0**（见[插件依赖关系](#插件依赖关系)）。本地配置：`image-generation.engine: gpt`（默认值），图片保存到 `dsh-image-gen/` 工作区目录。
- `@LiuRJ99/dsh-cpa-plugin`（`image-generation` 子路径）—— CPA 供应商同时暴露支持生图的模型协议（`gemini-3.1-flash-image`、`gpt-image-1.5`、`gpt-image-2`）· **已安装 v0.4.0** ✅

## Cost & Usage 用量与费用

- [dsh-spend](https://github.com/nonewind/dsh-spend) —— token 调用量、多维度统计、自动识别计费计划（Code/Token）与费用估算 · **已安装 v0.6.0** ✅

## Workflow 工作流

- [dsh-taskboard](https://github.com/LiuRJ99/dsh-taskboard-cloader) —— agent 优先的任务看板：host 侧任务账本 + `taskboard_*` agent 工具、项目认领边界、按任务指定模型在独立 Session 中执行、cron 定时调度、git worktree 隔离（独立任务分支、提交证据、一键合并）、SSE 实时看板，并支持与 `dsh-better-sidebar` 侧边栏页签可选集成 · **已安装 v0.6.0** ✅ —— [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的个人 fork，带本地改动（本地源码目录 `dsh-taskboard-cloader`）

## Web UI 界面增强

- [dsh-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) —— 类似 VSCode 的 DSH Web 侧边栏（文件资源管理器、编辑器、终端、Git、浏览器预览），按会话隔离；向其他插件开放 `ctx.betterSidebar` 服务（`registerTab` / `registerFileViewer`），支持动态注册页签与自定义预览器 · **已安装 v0.16.1** ✅（本地源码目录 `DSH-better-sidebar`）

## Compatibility 兼容层

- [dsh-sandbox-schema-shim](https://github.com/xiaohj233/dsh-compat-shims) —— 在 `danger-full-access` 会话中移除模型侧 shell / file 工具 schema 里冗余的沙箱提权字段 · **已安装 v0.1.1** ✅（`dsh-compat-shims` monorepo 中的 `dsh-sandbox-schema-shim` 包）

## 插件依赖关系

根据已安装包的 `dependencies` / `peerDependencies` 与源码 import 观察：

```text
dsh-image-gen 0.3.0 ────────(硬依赖)───────▶ @LiuRJ99/dsh-cpa-plugin ≥0.3.0 <0.5.0
                                            (import 了 '@LiuRJ99/dsh-cpa-plugin/image-generation')

@LiuRJ99/dsh-cpa-plugin 0.4.0 ───────────▶ @deepseek-ai/dsh-llm、dsh-credentials、dsh-settings
                                            (把 'CLIProxyAPI' 供应商注册进 llm-pi-ai)

@LiuRJ99/dsh-workbuddy-provider 0.2.0 ───▶ @deepseek-ai/dsh-llm、dsh-credentials、dsh-settings
                                            (把 'WorkBuddy' 供应商注册进 llm-pi-ai，本地端口 8318)

dsh-taskboard 0.6.0 ──────(可选集成)─────▶ dsh-better-sidebar ≥0.16.1
                                            (通过 ctx.betterSidebar 注册侧边栏页签)

dsh-better-sidebar ─────────────────────── 服务提供方：向其他插件开放 ctx.betterSidebar
                                            (registerTab / registerFileViewer)

@zibokapi/dsh-codex-computer-use 0.1.2 ─── 常驻 Swift 守护进程 + AX 树捕获 + SkyLight/CGEvent 后台输入；
                                            提供 computer_use_* 工具、审批门禁、独立 MCP 服务

@yuxianglin/dsh-bridge-browser ─────────── 浏览器桥接插件：WebSocket 通道 + browser_* 工具；
                                            与 Chrome/Firefox MV3 侧边栏扩展配对（扩展需另行安装）

dsh-taskboard ──────────────────────────── 核心自包含；支持与 dsh-better-sidebar 可选集成
dsh-spend ──────────────────────────────── 只依赖官方 @deepseek-ai/cordis、@deepseek-ai/dsh-home-paths、
                                            dsh-typert-protocol、schemastery
dsh-sandbox-schema-shim ────────────────── 独立兼容层，无第三方依赖
```

要点：

1. **`dsh-image-gen` 0.3.0 硬依赖 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0（<0.5.0）** —— 需先装 CPA 插件，否则图片生成无法解析依赖。
2. **`dsh-cpa-plugin`（0.4.0）与 `dsh-workbuddy-provider`（0.2.0）是平行的模型供应商插件**，都注册进官方 `llm-pi-ai` 供应商注册表（`CLIProxyAPI` 端口 8317、`WorkBuddy` 端口 8318），二者互不依赖。
3. **`dsh-better-sidebar` 是服务提供方** —— 其他插件可通过 `ctx.betterSidebar` 注册侧边栏页签 / 文件预览器；`dsh-taskboard` 0.6.0 已提供可选集成。
4. **`@zibokapi/dsh-codex-computer-use` 是独立的桌面自动化插件** —— 包含 macOS 常驻 Swift 守护进程，提供无障碍树捕获、后台输入模拟以及独立的 MCP 服务（`dsh-computer-mcp`）。
5. **`@yuxianglin/dsh-bridge-browser` 是独立的桥接插件** —— 与 Chrome/Firefox MV3 侧边栏扩展配对（扩展通过 `scripts/install.sh` 另行安装）。
6. **`dsh-taskboard`、`dsh-spend`、`dsh-sandbox-schema-shim` 核心自包含** —— 无强制插件间依赖。

## 安装

```bash
# 向 web profile 添加插件
dsh plugin --profile web add <包名或-github-引用>

# 示例：从 npm 安装（预构建产物）
dsh plugin --profile web add dsh-taskboard

# 示例：安装 Computer Use 插件并配置 macOS 守护进程与权限
dsh plugin --profile web add @zibokapi/dsh-codex-computer-use
npx @zibokapi/dsh-codex-computer-use

# 示例：从 GitHub monorepo 子路径安装
dsh plugin --profile web add "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
```

安装后重启 `dsh web` 并刷新页面。

## 许可证

本清单本身为 MIT。各插件保留各自许可证
（`dsh-taskboard` 为 Apache-2.0，其余为 MIT）。
