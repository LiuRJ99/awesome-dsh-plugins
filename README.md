<p align="center">
  <img src="assets/logo.png" alt="Awesome DSH Plugins" width="160">
</p>

<h1 align="center">Awesome DSH Plugins</h1>

<p align="center">
  Curated plugins, tools and extensions for <a href="https://github.com/deepseek-ai/deepseek-harness">DeepSeek Harness</a> / DSH.
</p>

<p align="center">
  <a href="README.zh-CN.md">简体中文</a>
</p>

This list is written from the plugins **actually installed** on a local DSH instance
(`~/.dsh/profiles/web` — the `dsh web` profile). Each entry notes the installed
version and the plugin-to-plugin dependencies observed locally.

## Contents

- [Installed Plugins](#installed-plugins)
- [Desktop Automation / Computer Use](#desktop-automation--computer-use)
- [Browser Control](#browser-control)
- [Model Providers](#model-providers)
- [Image Generation](#image-generation)
- [Cost & Usage](#cost--usage)
- [Workflow](#workflow)
- [Web UI](#web-ui)
- [Compatibility](#compatibility)
- [Plugin Dependencies](#plugin-dependencies)
- [Install](#install)
- [License](#license)

## Installed Plugins

Plugins currently installed in the local `web` profile
(`~/.dsh/profiles/web/package.json`):

| Plugin | Version | Type | Source |
| --- | --- | --- | --- |
| `@LiuRJ99/dsh-cpa-plugin` | 0.4.0 | Model provider (LLM) | [github.com/LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) |
| `@LiuRJ99/dsh-workbuddy-provider` | 0.2.0 | Model provider (LLM) | [github.com/LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) |
| `@yuxianglin/dsh-bridge-browser` | 0.0.4 | Browser control | [github.com/LiuRJ99/dsh-browser](https://github.com/LiuRJ99/dsh-browser) (fork of [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser); workspace/extension v0.1.3) |
| `@zibokapi/dsh-codex-computer-use` | 0.1.2 | Desktop automation / Computer use | [github.com/geohotstan/dsh-computer-use](https://github.com/geohotstan/dsh-computer-use) |
| `dsh-better-sidebar` | 0.16.1 | Web UI | [github.com/omdsh-dev/DSH-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) |
| `dsh-image-gen` | 0.3.0 | Image generation | [github.com/LiuRJ99/dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) (fork of [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen)) |
| `dsh-sandbox-schema-shim` | 0.1.1 | Compatibility shim | [github.com/xiaohj233/dsh-compat-shims](https://github.com/xiaohj233/dsh-compat-shims) |
| `dsh-spend` | 0.6.0 | Cost & usage | [github.com/nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) |
| `dsh-taskboard` | 0.6.0 | Workflow / task board | [github.com/LiuRJ99/dsh-taskboard-cloader](https://github.com/LiuRJ99/dsh-taskboard-cloader) (fork of [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard)) |

## Desktop Automation / Computer Use

- [@zibokapi/dsh-codex-computer-use](https://github.com/geohotstan/dsh-computer-use) —— desktop computer use for macOS (OpenAI Codex Computer Use parity): resident Swift helper daemon (`dsh-computer-daemon.app`), accessibility-tree window capture (`computer_use_get_app_state`), screenshots, synthesized mouse & keyboard input via private SkyLight (`SLEventPostToPid`) with `CGEvent.postToPid` fallback (background execution without focus stealing), per-app approval policy (`ctx.computer`), `computer-use` skill, and standalone MCP stdio server (`dsh-computer-mcp`) · **installed v0.1.2** ✅ (local source dir `dsh-computer-use`)

## Browser Control

- [dsh-browser](https://github.com/LiuRJ99/dsh-browser) —— connect DSH to the Chrome or Firefox tab you are already using: the `@yuxianglin/dsh-bridge-browser` bridge plugin (token-authenticated WebSocket carrier + text-only `browser_*` tools — `browser_snapshot` / `browser_click` / `browser_type` / `browser_navigate` / `browser_screenshot` / `browser_network_capture` and more) plus a Chrome/Firefox MV3 sidebar extension that preserves login state, session and cookies · **bridge installed v0.0.4, workspace/extension v0.1.3** ✅ — user's fork of [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) with Firefox MV3 support (local source dir `dsh-browser`)

## Model Providers

- [@LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) —— GUI-configurable CLIProxyAPI Responses provider for DeepSeek Harness with account management, quota window parsing (Codex 5-hour and weekly windows), speed preference extensions, and an `./image-generation` service subpath · **installed v0.4.0** ✅ (local source dir `dsh-cpa-plugin`)
- [@LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) —— seamless integration with local WorkBuddy built-in models running on local port 8318, registering into the `llm-pi-ai` provider registry · **installed v0.2.0** ✅ (local source dir `dsh-workbuddy-provider`)

## Image Generation

- [dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) —— image generation for DSH (GPT Image / Gemini Image) · **installed v0.3.0** ✅ — user's fork of [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen), CPA-adapter build; requires `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0 (see [Plugin Dependencies](#plugin-dependencies)). Local engine config: `image-generation.engine: gpt` (default), saving into `dsh-image-gen/` workspace folder.
- `@LiuRJ99/dsh-cpa-plugin` (`image-generation` subpath) —— the CPA provider also exposes image-capable models contract (`gemini-3.1-flash-image`, `gpt-image-1.5`, `gpt-image-2`) · **installed v0.4.0** ✅

## Cost & Usage

- [dsh-spend](https://github.com/nonewind/dsh-spend) —— token usage, multi-dimensional statistics, auto-detected billing plans (Code/Token) and estimated spend for the DSH web UI · **installed v0.6.0** ✅

## Workflow

- [dsh-taskboard](https://github.com/LiuRJ99/dsh-taskboard-cloader) —— agent-first task board for the DSH web GUI: host-authoritative ledger with `taskboard_*` agent tools, project claim boundaries, per-task model execution in fresh sessions, host-side cron scheduling, optional git-worktree code isolation (dedicated task branches, commit evidence, one-click merge), live SSE kanban view, and optional sidebar tab integration with `dsh-better-sidebar` · **installed v0.6.0** ✅ — user's fork of [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) with local modifications (local source dir `dsh-taskboard-cloader`)

## Web UI

- [dsh-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) —— a VSCode-like right sidebar for the DSH web GUI (explorer / editor / terminal / git / browser preview), session-isolated; exposes service `ctx.betterSidebar` (`registerTab` / `registerFileViewer`) for other plugins to register sidebar tabs and file viewers · **installed v0.16.1** ✅ (local source dir `DSH-better-sidebar`)

## Compatibility

- [dsh-sandbox-schema-shim](https://github.com/xiaohj233/dsh-compat-shims) —— removes redundant sandbox escalation fields from model-facing shell / file tool schemas in `danger-full-access` sessions · **installed v0.1.1** ✅ (package `dsh-sandbox-schema-shim` from the `dsh-compat-shims` monorepo)

## Plugin Dependencies

Observed from the installed packages' `dependencies` / `peerDependencies` and source imports:

```text
dsh-image-gen 0.3.0 ────────(hard)───────▶ @LiuRJ99/dsh-cpa-plugin ≥0.3.0 <0.5.0
                                            (imports '@LiuRJ99/dsh-cpa-plugin/image-generation')

@LiuRJ99/dsh-cpa-plugin 0.4.0 ───────────▶ @deepseek-ai/dsh-llm, dsh-credentials, dsh-settings
                                            (registers 'CLIProxyAPI' provider into llm-pi-ai)

@LiuRJ99/dsh-workbuddy-provider 0.2.0 ───▶ @deepseek-ai/dsh-llm, dsh-credentials, dsh-settings
                                            (registers 'WorkBuddy' provider, local port 8318)

dsh-taskboard 0.6.0 ──────(optional)─────▶ dsh-better-sidebar ≥0.16.1
                                            (optional sidebar tab integration via ctx.betterSidebar)

dsh-better-sidebar ─────────────────────── service provider: exposes ctx.betterSidebar
                                            (registerTab / registerFileViewer) for other plugins

@zibokapi/dsh-codex-computer-use 0.1.2 ─── resident Swift daemon + AX tree + SkyLight/CGEvent input;
                                            provides computer_use_* tools, approval policy, MCP server

@yuxianglin/dsh-bridge-browser ─────────── browser bridge plugin: WebSocket carrier +
                                            browser_* tools; pairs with Chrome/Firefox
                                            MV3 sidebar extension (out-of-band install)

dsh-spend ──────────────────────────────── depends on official @deepseek-ai/cordis,
                                            @deepseek-ai/dsh-home-paths,
                                            dsh-typert-protocol, schemastery only
dsh-sandbox-schema-shim ────────────────── standalone shim, no third-party deps
```

Key takeaways:

1. **`dsh-image-gen` 0.3.0 has a hard dependency on `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0 (<0.5.0)** — install the CPA plugin first, otherwise image generation fails to resolve.
2. **`dsh-cpa-plugin` (0.4.0) and `dsh-workbuddy-provider` (0.2.0) are sibling model-provider plugins** registered into the official `llm-pi-ai` provider registry (`CLIProxyAPI` on port 8317, `WorkBuddy` on port 8318). They are independent of each other.
3. **`dsh-better-sidebar` is a service provider** — plugins may register sidebar tabs / file viewers through `ctx.betterSidebar`. `dsh-taskboard` 0.6.0 integrates with it optionally.
4. **`@zibokapi/dsh-codex-computer-use` is a standalone desktop automation plugin** — brings macOS accessibility tree capture, screenshots, background input simulation, and a standalone MCP server (`dsh-computer-mcp`).
5. **`@yuxianglin/dsh-bridge-browser` is a standalone bridge plugin** — pairs with the Chrome/Firefox MV3 sidebar extension (installed out-of-band via `scripts/install.sh`).
6. **`dsh-taskboard`, `dsh-spend` and `dsh-sandbox-schema-shim` are self-contained** with no mandatory inter-plugin dependencies.

## Install

```bash
# Add a plugin to the web profile
dsh plugin --profile web add <package-or-github-ref>

# Example: install from npm (prebuilt)
dsh plugin --profile web add dsh-taskboard

# Example: install computer-use plugin and setup daemon
dsh plugin --profile web add @zibokapi/dsh-codex-computer-use
npx @zibokapi/dsh-codex-computer-use

# Example: install from GitHub monorepo subpath
dsh plugin --profile web add "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
```

Restart `dsh web` and refresh the page after installing.

## License

The listing itself is MIT. Each plugin keeps its own license
(`dsh-taskboard` is Apache-2.0, the others are MIT).
