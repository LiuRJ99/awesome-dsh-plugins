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
- [Security & Capability Control 安全与门控](#security--capability-control-安全与门控)
- [Compatibility 兼容层](#compatibility-兼容层)
- [插件依赖关系](#插件依赖关系)
  - [Peer 依赖解析审计](#peer-依赖解析审计)
- [安装](#安装)
  - [本地安装结构](#本地安装结构)
- [许可证](#许可证)

## 已安装插件

当前安装在本地 `web` profile（`~/.dsh/profiles/web/package.json`）的插件：

| 插件 | 版本 | 类型 | 安装来源 |
| --- | --- | --- | --- |
| `@LiuRJ99/dsh-cpa-plugin` | 0.4.0 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin)（[router-for-me/dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider) 的 fork；同步源版本 v0.0.1） |
| `@LiuRJ99/dsh-workbuddy-provider` | 0.2.1 | 模型供应商（LLM） | [github.com/LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) |
| `@yuxianglin/dsh-bridge-browser` | 0.0.5 | 浏览器控制 | [github.com/LiuRJ99/dsh-browser](https://github.com/LiuRJ99/dsh-browser)（[Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) 的 fork；同步源版本 workspace v0.1.2 / bridge v0.0.3；当前 workspace/扩展 v0.1.4，实际加载的 dist v0.1.3） |
| `@zibokapi/dsh-codex-computer-use` | 0.1.2 | 桌面自动化 / 电脑控制 | [github.com/geohotstan/dsh-computer-use](https://github.com/geohotstan/dsh-computer-use) |
| `dsh-better-sidebar` | 0.17.1 | Web UI | [github.com/omdsh-dev/DSH-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) |
| `dsh-image-gen` | 0.4.1 | 图片生成 | [github.com/LiuRJ99/dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen)（[shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的 fork；同步源版本 v0.1.7） |
| `dsh-sandbox-schema-shim` | 0.1.1 | 兼容层 | [github.com/xiaohj233/dsh-compat-shims](https://github.com/xiaohj233/dsh-compat-shims) |
| `dsh-spend` | 0.6.2 | 用量与费用 | [github.com/nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) |
| `dsh-taskboard` | 0.6.0 | 工作流 / 任务看板 | [github.com/LiuRJ99/dsh-taskboard-cloader](https://github.com/LiuRJ99/dsh-taskboard-cloader)（[cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的 fork；同步源版本 v0.5.1） |
| `dsh-tool-lazy-gate` | 0.1.0 | 安全与门控 | [github.com/LiuRJ99/dsh-tool-lazy-gate](https://github.com/LiuRJ99/dsh-tool-lazy-gate) |

这十个插件在 `~/.dsh/profiles/web/package.json` 的 `dsh.profile.bundles` 与
`dependencies` 中均已登记（两份清单无差异）。其中七个以 `link:` 方式指向
`~/dsh-work` 下的本地 checkout；`dsh-better-sidebar`（`^0.17.1`）与
`dsh-spend`（`^0.6.2`）来自 npm，`dsh-sandbox-schema-shim` 来自 GitHub monorepo
子路径。

以上结果基于宿主 `@deepseek-ai/dsh` **0.1.0-rc.8** 校验（`cordis` 4.0.1、
`react` 18.3.1、`sharp` 0.35.3）。

## Desktop Automation 桌面控制

- [@zibokapi/dsh-codex-computer-use](https://github.com/geohotstan/dsh-computer-use) —— 专为 macOS 打造的桌面 Computer Use（对标 OpenAI Codex Computer Use）：常驻 Swift 辅助守护进程（`dsh-computer-daemon.app`）、无障碍树（Accessibility Tree）窗口捕获（`computer_use_get_app_state`）、屏幕截图、基于 SkyLight（`SLEventPostToPid`）与 `CGEvent.postToPid` 回退的后台鼠标/键盘输入模拟（无需切到前台/不抢焦点）、应用级审批门禁（`ctx.computer`）、`computer-use` skill 以及独立的 MCP stdio 服务（`dsh-computer-mcp`）· **已安装 v0.1.2** ✅（本地源码目录 `dsh-computer-use`）

## Browser Control 浏览器控制

- [dsh-browser](https://github.com/LiuRJ99/dsh-browser) —— 让 DSH 连接你正在使用的 Chrome 或 Firefox 标签页：`@yuxianglin/dsh-bridge-browser` 桥接插件（token 认证的 WebSocket 通道 + 纯文本 `browser_*` 工具）+ Chrome/Firefox MV3 侧边栏扩展，保留登录态、会话与 cookie；在 Skill 注册表中注册 `/browser` 授权指令 · **桥接已安装 v0.0.5，workspace/扩展 v0.1.4** ✅ —— [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) 的个人 fork（同步源版本 workspace v0.1.2 / bridge v0.0.3），新增 Firefox MV3 支持（本地源码目录 `dsh-browser`）

  桥接插件共暴露 20 个工具：`browser_snapshot`、`browser_click`、`browser_type`、
  `browser_upload_file`、`browser_press`、`browser_scroll`、`browser_navigate`、
  `browser_back`、`browser_forward`、`browser_reload`、`browser_get_text`、
  `browser_wait`、`browser_screenshot`、`browser_click_text`、`browser_wait_for`、
  `browser_get_table`、`browser_eval`、`browser_download_wait`、
  `browser_network_capture`、`browser_list_tabs`。其中 `browser` skill 标记为
  `model-invocable: false` —— 只有用户主动输入 `/browser` 才能解锁。

  > **本地版本偏差提示：** 实际从 `~/.dsh/browser-extension` 加载的已构建扩展是
  > **v0.1.3**，比 workspace 源码的 v0.1.4 落后一个补丁版本 —— 重新执行
  > `scripts/install.sh` 即可重建并同步 dist。该脚本同时会把仓库复制到
  > `~/.dsh/dsh-browser`（这份托管副本仍是桥接 **0.0.3** / 扩展 **0.1.2**）；
  > profile 实际链接的是 `~/dsh-work` 的 checkout，因此这份托管副本并不生效。

## Model Providers 模型供应商

- [@LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) —— 支持 Web GUI 动态配置的 CLIProxyAPI Responses 供应商，提供多账号管理、额度窗口解析（Codex 5 小时与周额度窗口合并计算）、模型响应速度偏好扩展以及 `./image-generation` 生图服务子路径 · **已安装 v0.4.0** ✅ —— [router-for-me/dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider) 的个人 fork（同步源版本 v0.0.1，本地源码目录 `dsh-cpa-plugin`）。向 `llm-pi-ai` 注册 `CLIProxyAPI` 供应商（`api: openai-responses`，base URL `http://127.0.0.1:8317/v1`，共 21 个模型），其中包含 `dsh-image-gen` 所依赖的三个 `imageGeneration: true` 模型（`gemini-3.1-flash-image`、`gpt-image-1.5`、`gpt-image-2`）。本地配置 `dsh-cpa-plugin.refreshIntervalMs: 300000`。
- [@LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) —— 无缝接入本地 WorkBuddy 内置模型（本地端口 8318），注册进官方 `llm-pi-ai` 模型注册表；提供自定义小伴机器人导航图标与现代化的设置面板布局 · **已安装 v0.2.1** ✅（本地源码目录 `dsh-workbuddy-provider`）。向 `llm-pi-ai` 注册 `WorkBuddy`（`api: openai-completions`，base URL `http://127.0.0.1:8318/v1`，共 13 个模型）；本地启用 `autoRegisterModels: true` 并选中 13 个模型。它同时也是本地**默认 agent 模型供应商**（`agent-default-model.provider: WorkBuddy`，模型 `hy4-preview`，`reasoningEffort: high`）。

  > **本地版本偏差提示：** 该插件把 `@deepseek-ai/*` peer 精确锁定在
  > `0.1.0-rc.6`，而宿主提供的是 **0.1.0-rc.8**，因此有四个 peer
  > （`dsh-llm`、`dsh-llm-pi-ai`、`dsh-settings`、`dsh-credentials`）形式上不满足。
  > 实际不影响运行 —— 这些包由宿主提供，且该插件真正的运行时依赖只有
  > `@deepseek-ai/schemastery`。

## Image Generation 图片生成

- [dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) —— CPA 驱动的图片生成（GPT Image 2 / Gemini Image）；具备服务端高性能 Sharp WebP 缩略图生成与 HTTP 长效缓存（`public, max-age=604800, immutable`）、画廊虚拟化滚动窗口化渲染（Grid / List / Table 多视图）、图文解耦按需加载、多维度排序、图片比例筛选、分类标签徽标与工作区自动保存（`dsh-image-gen/`）· **已安装 v0.4.1** ✅ —— [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 的个人 fork（CPA 适配版，同步源版本 v0.1.7），**需要 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0** 与宿主 `sharp` peer 依赖（见[插件依赖关系](#插件依赖关系)）。本地配置：`image-generation.engine: gpt`（默认值）。

  > **本地版本偏差提示：** `sharp` 声明为 `^0.35.4`，但 DSH 宿主实际提供的是
  > **0.35.3**，因此该 peer 形式上不满足。实践中插件仍会解析到宿主那份
  > `sharp`，缩略图功能正常 —— 因为 `sharp` 是以顶层 ESM
  > `import sharp from 'sharp'` 引入的，由宿主提供而非按版本门控。最干净的
  > 修复方式仍是把宿主 `sharp` 升级到 ≥ 0.35.4。
- `@LiuRJ99/dsh-cpa-plugin`（`image-generation` 子路径）—— CPA 供应商同时暴露支持生图的模型协议（`gemini-3.1-flash-image`、`gpt-image-1.5`、`gpt-image-2`）· **已安装 v0.4.0** ✅

## Cost & Usage 用量与费用

- [dsh-spend](https://github.com/nonewind/dsh-spend) —— token 调用量、多维度统计、自动识别计费计划（Code/Token，包含 MiniMax Token Plan）、规范化 Provider 发现与费用估算 · **已安装 v0.6.2** ✅

## Workflow 工作流

- [dsh-taskboard](https://github.com/LiuRJ99/dsh-taskboard-cloader) —— agent 优先的任务看板：host 侧任务账本 + `taskboard_*` agent 工具、项目认领边界、按任务指定模型在独立 Session 中执行、cron 定时调度、git worktree 隔离（独立任务分支、提交证据、一键合并）、SSE 实时看板、评论模型首字母 SVG 头像、Mermaid 懒加载、文件链接可点击，并支持与 `dsh-better-sidebar` 侧边栏页签可选集成 · **已安装 v0.6.0** ✅ —— [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 的个人 fork（同步源版本 v0.5.1），带本地改动（本地源码目录 `dsh-taskboard-cloader`）

  > **本地版本偏差提示：** `dsh-better-sidebar` peer 声明为 `^0.16.1`，但本地
  > 安装的是 **0.17.1**，不在该 caret 范围内。不过它在
  > `peerDependenciesMeta` 中标记为 `optional: true`，且降级处理做得很完整：
  > 页签模块对该包只做**类型导入**，运行时通过 `ctx.get('betterSidebar')`
  > 获取服务，若服务或 `registerTab` 不存在则回退到传统 DOM 挂载。其运行时
  > 依赖只有 `marked` 与 `mermaid`。

## Web UI 界面增强

- [dsh-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) —— 类似 VSCode 的 DSH Web 侧边栏（文件资源管理器、编辑器、终端、Git、浏览器预览），按会话隔离；支持固定终端与 TabBar 内联固定标签页；向其他插件开放 `ctx.betterSidebar` 服务（`registerTab` / `registerFileViewer`），支持动态注册页签与自定义预览器 · **已安装 v0.17.1** ✅ —— 从 npm 解析（`^0.17.1`）；本地**没有**源码 checkout，这点与本文档早期版本的说法不同。内置 CodeMirror 6 语言包（18 种语言）、`mermaid`、`node-pty`、`rxjs` 与 `ws`。其可选 peer `@huanlin/dsh-plugin-better-locale` **未安装**（可选，无影响）。

## Security & Capability Control 安全与门控

- [dsh-tool-lazy-gate](https://github.com/LiuRJ99/dsh-tool-lazy-gate) —— DSH 会话级能力门控：默认隐藏并拦截 `browser_*` 与 `computer_use_*` 等高特权工具家族，直到用户在当前会话显式发起匹配的 Skill 指令（`/browser`、`/computer-use`）才解锁；采用动态工具隐藏（`tools.restrict`）与防绕过执行拦截（`tools.guard`）双层防御，自动抑制未解锁时的 System Prompt 引导片段，支持会话恢复时从持久化 `user/message` 日志重建解锁状态，并提供 GUI 可视化设置（`tool-lazy-gate` 命名空间）· **已安装 v0.1.0** ✅（本地源码目录 `dsh-tool-lazy-gate`）

  插件内置只有 **两个** 兜底能力（`browser`、`computer`），但本地
  `~/.dsh/settings.yaml` 配置了**第三个**：`taskboard`
  （`toolPrefixes: [taskboard_]`、`promptSections: [plugin:dsh-taskboard]`），
  因此本地实际门控了三个工具家族。`taskboard` 在运行时还有特殊处理：插件暴露了
  `grant-taskboard` RPC 端点，允许回环 GUI 代码（例如任务看板的"新建"面板）
  仅对经过校验的活跃会话授予该能力 —— 而 browser / computer 的授权始终由宿主侧
  决定，绝不受客户端控制。

## Compatibility 兼容层

- [dsh-sandbox-schema-shim](https://github.com/xiaohj233/dsh-compat-shims) —— 在 `danger-full-access` 会话中移除模型侧 shell / file 工具 schema 里冗余的沙箱提权字段 · **已安装 v0.1.1** ✅（`dsh-compat-shims` monorepo 中的 `dsh-sandbox-schema-shim` 包）。完全独立：**零**依赖、**零** peer 依赖。

## 插件依赖关系

根据已安装包的 `dependencies` / `peerDependencies` / `peerDependenciesMeta`
与源码 import 观察，并对照宿主 `@deepseek-ai/dsh` **0.1.0-rc.8** 解析。

以下是唯一真正存在的**插件对插件**依赖边 —— 其余都是宿主提供的
`@deepseek-ai/*` peer，由运行中的 DSH 宿主满足：

```text
dsh-image-gen 0.4.1 ───(硬依赖，已满足)──▶ @LiuRJ99/dsh-cpa-plugin >=0.3.0 <0.5.0  (0.4.0 OK)
                                             import 了 '@LiuRJ99/dsh-cpa-plugin/image-generation'
                                            ▶ 宿主 peer: sharp ^0.35.4  (宿主为 0.35.3 — 偏差)

dsh-taskboard 0.6.0 ──(可选，偏差)─────▶ dsh-better-sidebar ^0.16.1  (0.17.1，超出范围)
                                             optional: true；仅类型导入 +
                                             ctx.get('betterSidebar') + 传统 DOM 回退
                                            ▶ 运行时依赖: marked、mermaid

dsh-better-sidebar 0.17.1 ──────────────── 服务提供方：暴露 ctx.betterSidebar
                                             (registerTab / registerFileViewer)
                                            ▶ 可选 peer @huanlin/dsh-plugin-better-locale
                                               ^0.1.0 — 未安装（可选，无影响）

dsh-tool-lazy-gate 0.1.0 ───────────────── 能力门控：挂载 tools.restrict + tools.guard
                                             + system prompt。本地门控三个工具家族：
                                             browser_*、computer_use_*、taskboard_*
                                             (taskboard_ 来自 settings.yaml，非内置)

@zibokapi/dsh-codex-computer-use 0.1.2 ──── 常驻 Swift 守护进程 + AX 树 + SkyLight/CGEvent
                                             输入模拟；computer_use_* 工具、审批门禁、
                                             MCP 服务；受 dsh-tool-lazy-gate 门控
                                            ▶ bins: dsh-codex-computer-use、dsh-computer-mcp

@yuxianglin/dsh-bridge-browser 0.0.5 ────── WebSocket 通道 + 20 个 browser_* 工具；与
                                             Chrome/Firefox MV3 侧边栏扩展配对；
                                             受 dsh-tool-lazy-gate 门控（需 /browser）
                                            ▶ 依赖: ws、schemastery

@LiuRJ99/dsh-cpa-plugin 0.4.0 ───────────── 注册 'CLIProxyAPI' 进 llm-pi-ai
                                             (openai-responses, :8317, 21 个模型)
@LiuRJ99/dsh-workbuddy-provider 0.2.1 ───── 注册 'WorkBuddy' 进 llm-pi-ai
                                             (openai-completions, :8318, 13 个模型)
                                            ▶ peer 锁定 0.1.0-rc.6，宿主为 rc.8 — 偏差
                                            ▶ 二者互不依赖

dsh-spend 0.6.2 ─────────────────────────── 仅有 peer：@deepseek-ai/cordis、dsh-credentials、
                                             dsh-home-paths、dsh-typert-protocol、schemastery
                                            ▶ 零运行时依赖
dsh-sandbox-schema-shim 0.1.1 ───────────── 零依赖、零 peer 依赖
```

### Peer 依赖解析审计

对全部十个插件用 semver（`includePrerelease`）程序化校验的结果：

| 插件 | 强制 peer 数 | 结果 |
| --- | --- | --- |
| `@zibokapi/dsh-codex-computer-use` 0.1.2 | 11 | 全部满足 |
| `dsh-spend` 0.6.2 | 5 | 全部满足 |
| `dsh-tool-lazy-gate` 0.1.0 | 5 | 全部满足 |
| `dsh-sandbox-schema-shim` 0.1.1 | 0 | 不适用 |
| `dsh-image-gen` 0.4.1 | 19 | 1 处偏差（`sharp`） |
| `@LiuRJ99/dsh-cpa-plugin` 0.4.0 | 13 | 2 个缺失（`dsh-client-ui-primitives`、`dsh-client-ui-slots`） |
| `@LiuRJ99/dsh-workbuddy-provider` 0.2.1 | 6 | 4 处偏差（锁定 `rc.6`，宿主为 `rc.8`） |
| `@yuxianglin/dsh-bridge-browser` 0.0.5 | 9 | 8 处偏差（`^0.1.1-rc.1` vs 宿主 `0.1.0-rc.8`） |
| `dsh-taskboard` 0.6.0 | 1（可选） | 1 处偏差 —— 可选且可优雅降级 |
| `dsh-better-sidebar` 0.17.1 | 17 | 3 个缺失（`dsh-client-ui-primitives`、`dsh-client-ui-slots`、`react-dom`） |

这些都不会导致故障。所有 `@deepseek-ai/*` 包都由宿主以 **0.1.0-rc.8** 提供，
`sharp`（0.35.3）、`react`（18.3.1）与 `react-dom` 也都能从宿主自带的
`node_modules` 解析。那几个 `dsh-client-ui-*` 包在磁盘上确实不存在同名目录
—— 它们是通过各插件 `package.json` 中声明的 `dsh.client.inject` 清单满足的
（`cpa-plugin` 注入 9 个、`image-gen` 8 个、`better-sidebar` 5 个），即由宿主
组合而非 npm 安装。因此这些警告应理解为"版本锁定相对宿主已过期"，而不是
"安装已损坏"。

要点：

1. **`dsh-image-gen` 0.4.1 是唯一存在硬插件间依赖的插件**：依赖 `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0（<0.5.0）—— 需先装 CPA 插件，否则图片生成无法解析依赖。其 `sharp` peer（`^0.35.4`）比宿主的 0.35.3 高一个补丁版本，但由于 `sharp` 由宿主提供，缩略图功能仍然正常。
2. **`dsh-cpa-plugin`（0.4.0）与 `dsh-workbuddy-provider`（0.2.1）是平行的模型供应商插件**，都注册进官方 `llm-pi-ai` 供应商注册表（`CLIProxyAPI` 端口 8317、`WorkBuddy` 端口 8318），二者互不依赖；WorkBuddy 还是本地配置的默认 agent 供应商。
3. **`dsh-better-sidebar`（0.17.1）是服务提供方** —— 其他插件可通过 `ctx.betterSidebar` 注册侧边栏页签 / 文件预览器；`dsh-taskboard` 0.6.0 已提供可选集成，且在 peer 范围不满足时能优雅降级。
4. **`dsh-tool-lazy-gate`（0.1.0）提供会话级能力门控** —— 本地门控 `browser_*`、`computer_use_*` **以及** `taskboard_*`，尽管内置默认值只有前两个。模型无法通过 `skill()` 工具自解锁；`taskboard_*` 另有 GUI 侧的 `grant-taskboard` RPC 授权路径。
5. **`@zibokapi/dsh-codex-computer-use` 是独立的桌面自动化插件** —— macOS 常驻 Swift 守护进程（`dsh-computer-daemon.app`），提供无障碍树捕获、后台输入模拟以及独立的 MCP 服务（`dsh-computer-mcp`）。
6. **`@yuxianglin/dsh-bridge-browser` 是独立的桥接插件** —— 基于 token 认证 WebSocket 的 20 个 `browser_*` 工具，与通过 `scripts/install.sh` 另行安装的 Chrome/Firefox MV3 侧边栏扩展配对。
7. **`dsh-taskboard`、`dsh-spend`、`dsh-sandbox-schema-shim` 核心自包含** —— 无强制插件间依赖。

## 安装

```bash
# 向 web profile 添加插件
dsh plugin --profile web add <包名或-github-引用>

# 示例：从 npm 安装（预构建产物）
dsh plugin --profile web add dsh-taskboard

# 示例：安装工具能力门控插件
dsh plugin --profile web add github:LiuRJ99/dsh-tool-lazy-gate

# 示例：安装 Computer Use 插件并配置 macOS 守护进程与权限
dsh plugin --profile web add @zibokapi/dsh-codex-computer-use
npx @zibokapi/dsh-codex-computer-use

# 示例：从 GitHub monorepo 子路径安装
dsh plugin --profile web add "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
```

安装后重启 `dsh web` 并刷新页面。

### 本地安装结构

十个插件中有七个是 `link:` 到本地 checkout 的，而非从 registry 安装 —— 这也
是本地改动无需重新发布即可生效的原因：

```jsonc
// ~/.dsh/profiles/web/package.json（节选）
"dependencies": {
  "@LiuRJ99/dsh-cpa-plugin":          "link:/Users/liurenjie/dsh-work/dsh-cpa-plugin",
  "@LiuRJ99/dsh-workbuddy-provider":  "link:/Users/liurenjie/dsh-work/dsh-workbuddy-provider",
  "@yuxianglin/dsh-bridge-browser":   "link:/Users/liurenjie/dsh-work/dsh-browser/packages/browser/bridge-browser",
  "@zibokapi/dsh-codex-computer-use": "link:/Users/liurenjie/dsh-work/dsh-computer-use",
  "dsh-image-gen":                    "link:/Users/liurenjie/dsh-work/dsh-image-gen",
  "dsh-taskboard":                    "link:/Users/liurenjie/dsh-work/dsh-taskboard-cloader",
  "dsh-tool-lazy-gate":               "link:/Users/liurenjie/dsh-work/dsh-tool-lazy-gate",
  "dsh-better-sidebar":               "^0.17.1",
  "dsh-spend":                        "^0.6.2",
  "dsh-sandbox-schema-shim":          "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
}
```

只有一处安装顺序有要求：先装 `@LiuRJ99/dsh-cpa-plugin`，再装 `dsh-image-gen`
—— 因为后者硬依赖前者。

## 许可证

本清单本身为 MIT。各插件保留各自许可证 —— 已根据已安装的 `package.json`
逐一核对：`dsh-taskboard` 为 **Apache-2.0**，其余九个为 **MIT**。