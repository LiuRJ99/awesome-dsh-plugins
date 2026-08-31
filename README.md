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
- [Security & Capability Control](#security--capability-control)
- [Compatibility](#compatibility)
- [Plugin Dependencies](#plugin-dependencies)
  - [Peer-resolution audit](#peer-resolution-audit)
- [Install](#install)
  - [How this local install is wired](#how-this-local-install-is-wired)
- [License](#license)

## Installed Plugins

Plugins currently installed in the local `web` profile
(`~/.dsh/profiles/web/package.json`):

| Plugin | Version | Type | Source |
| --- | --- | --- | --- |
| `@LiuRJ99/dsh-cpa-plugin` | 0.4.0 | Model provider (LLM) | [github.com/LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) (fork of [router-for-me/dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider); synced from v0.0.1) |
| `@LiuRJ99/dsh-workbuddy-provider` | 0.2.1 | Model provider (LLM) | [github.com/LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) |
| `@yuxianglin/dsh-bridge-browser` | 0.0.5 | Browser control | [github.com/LiuRJ99/dsh-browser](https://github.com/LiuRJ99/dsh-browser) (fork of [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser); synced from workspace v0.1.2 / bridge v0.0.3; current workspace/extension v0.1.4, loaded dist v0.1.3) |
| `@zibokapi/dsh-codex-computer-use` | 0.1.2 | Desktop automation / Computer use | [github.com/geohotstan/dsh-computer-use](https://github.com/geohotstan/dsh-computer-use) |
| `dsh-better-sidebar` | 0.17.1 | Web UI | [github.com/omdsh-dev/DSH-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) |
| `dsh-image-gen` | 0.4.1 | Image generation | [github.com/LiuRJ99/dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) (fork of [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen); synced from v0.1.7) |
| `dsh-sandbox-schema-shim` | 0.1.1 | Compatibility shim | [github.com/xiaohj233/dsh-compat-shims](https://github.com/xiaohj233/dsh-compat-shims) |
| `dsh-spend` | 0.6.2 | Cost & usage | [github.com/nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) |
| `dsh-taskboard` | 0.6.0 | Workflow / task board | [github.com/LiuRJ99/dsh-taskboard-cloader](https://github.com/LiuRJ99/dsh-taskboard-cloader) (fork of [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard); synced from v0.5.1) |
| `dsh-tool-lazy-gate` | 0.1.0 | Security & capability control | [github.com/LiuRJ99/dsh-tool-lazy-gate](https://github.com/LiuRJ99/dsh-tool-lazy-gate) |

All ten are registered both in `dsh.profile.bundles` and `dependencies` of
`~/.dsh/profiles/web/package.json` (no drift between the two lists). Seven are
pinned as local `link:` checkouts under `~/dsh-work`; `dsh-better-sidebar`
(`^0.17.1`) and `dsh-spend` (`^0.6.2`) resolve to npm, and
`dsh-sandbox-schema-shim` resolves to a GitHub monorepo subpath.

Verified against host `@deepseek-ai/dsh` **0.1.0-rc.8** (`cordis` 4.0.1,
`react` 18.3.1, `sharp` 0.35.3).

## Desktop Automation / Computer Use

- [@zibokapi/dsh-codex-computer-use](https://github.com/geohotstan/dsh-computer-use) —— desktop computer use for macOS (OpenAI Codex Computer Use parity): resident Swift helper daemon (`dsh-computer-daemon.app`), accessibility-tree window capture (`computer_use_get_app_state`), screenshots, synthesized mouse & keyboard input via private SkyLight (`SLEventPostToPid`) with `CGEvent.postToPid` fallback (background execution without focus stealing), per-app approval policy (`ctx.computer`), `computer-use` skill, and standalone MCP stdio server (`dsh-computer-mcp`) · **installed v0.1.2** ✅ (local source dir `dsh-computer-use`)

## Browser Control

- [dsh-browser](https://github.com/LiuRJ99/dsh-browser) —— connect DSH to the Chrome or Firefox tab you are already using: the `@yuxianglin/dsh-bridge-browser` bridge plugin (token-authenticated WebSocket carrier + text-only `browser_*` tools) plus a Chrome/Firefox MV3 sidebar extension that preserves login state, session and cookies; registers the `/browser` authorization skill in the skill registry · **bridge installed v0.0.5, workspace/extension v0.1.4** ✅ — user's fork of [Lum1104/dsh-browser](https://github.com/Lum1104/dsh-browser) (synced from workspace v0.1.2 / bridge v0.0.3) with Firefox MV3 support (local source dir `dsh-browser`)

  The bridge exposes 20 tools: `browser_snapshot`, `browser_click`, `browser_type`,
  `browser_upload_file`, `browser_press`, `browser_scroll`, `browser_navigate`,
  `browser_back`, `browser_forward`, `browser_reload`, `browser_get_text`,
  `browser_wait`, `browser_screenshot`, `browser_click_text`, `browser_wait_for`,
  `browser_get_table`, `browser_eval`, `browser_download_wait`,
  `browser_network_capture`, `browser_list_tabs`. The `browser` skill is
  `model-invocable: false` — only the user's `/browser` gesture can unlock it.

  > **Local skew note:** the built extension actually loaded from
  > `~/.dsh/browser-extension` is **v0.1.3**, one patch behind the v0.1.4
  > workspace source — rerun `scripts/install.sh` to rebuild and resync the dist.
  > The same `install.sh` also copies the repo to `~/.dsh/dsh-browser` (that
  > managed copy is still on bridge **0.0.3** / extension **0.1.2**); the
  > profile links the `~/dsh-work` checkout, so the managed copy is inert.

## Model Providers

- [@LiuRJ99/dsh-cpa-plugin](https://github.com/LiuRJ99/dsh-cpa-plugin) —— GUI-configurable CLIProxyAPI Responses provider for DeepSeek Harness with account management, quota window parsing (Codex 5-hour and weekly windows), speed preference extensions, and an `./image-generation` service subpath · **installed v0.4.0** ✅ — user's fork of [router-for-me/dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider) (synced from v0.0.1, local source dir `dsh-cpa-plugin`). Registers the `CLIProxyAPI` provider into `llm-pi-ai` with `api: openai-responses`, base URL `http://127.0.0.1:8317/v1`, and 21 models — including the three `imageGeneration: true` entries (`gemini-3.1-flash-image`, `gpt-image-1.5`, `gpt-image-2`) that `dsh-image-gen` drives. Local `dsh-cpa-plugin.refreshIntervalMs: 300000`.
- [@LiuRJ99/dsh-workbuddy-provider](https://github.com/LiuRJ99/dsh-workbuddy-provider) —— seamless integration with local WorkBuddy built-in models running on local port 8318, registering into the `llm-pi-ai` provider registry; features custom companion robot nav icon and modernized settings layout · **installed v0.2.1** ✅ (local source dir `dsh-workbuddy-provider`). Registers `WorkBuddy` with `api: openai-completions`, base URL `http://127.0.0.1:8318/v1` and 13 models; locally enabled with `autoRegisterModels: true` and 13 selected models. This is also the **default agent model provider** locally (`agent-default-model.provider: WorkBuddy`, model `hy4-preview`, `reasoningEffort: high`).

  > **Local skew note:** this plugin pins its `@deepseek-ai/*` peers to the
  > exact `0.1.0-rc.6` release while the host ships **0.1.0-rc.8**, so four
  > peers (`dsh-llm`, `dsh-llm-pi-ai`, `dsh-settings`, `dsh-credentials`) read
  > as unsatisfied. It works regardless — these are host-provisioned and the
  > plugin's only real runtime dependency is `@deepseek-ai/schemastery`.

## Image Generation

- [dsh-image-gen](https://github.com/LiuRJ99/dsh-image-gen) —— CPA-backed image generation for DSH (GPT Image 2 / Gemini Image); features server-side high-performance Sharp WebP thumbnail generation & HTTP caching (`public, max-age=604800, immutable`), gallery virtualization & windowed rendering (Grid / List / Table views), decoupled thumbnail vs full-resolution loading, multi-dimensional sorting, aspect ratio filtering, category pills with count badges, and saving to workspace (`dsh-image-gen/`) · **installed v0.4.1** ✅ — user's fork of [shanliuling/dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) (synced from v0.1.7); requires `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0 (<0.5.0) and host `sharp` peer dependency (see [Plugin Dependencies](#plugin-dependencies)). Local engine config: `image-generation.engine: gpt` (default).

  > **Local skew note:** `sharp` is declared as `^0.35.4`, but the DSH host
  > ships **0.35.3**, so this peer is formally unsatisfied. In practice the
  > plugin still resolves the host copy and thumbnailing works — `sharp` is a
  > plain top-level ESM import (`import sharp from 'sharp'`), so loading is
  > host-provided rather than version-gated. Still, upgrading the host `sharp`
  > to ≥ 0.35.4 is the clean fix.
- `@LiuRJ99/dsh-cpa-plugin` (`image-generation` subpath) —— the CPA provider also exposes image-capable models contract (`gemini-3.1-flash-image`, `gpt-image-1.5`, `gpt-image-2`) · **installed v0.4.0** ✅

## Cost & Usage

- [dsh-spend](https://github.com/nonewind/dsh-spend) —— token usage, multi-dimensional statistics, auto-detected billing plans (Code/Token, including MiniMax Token Plan), canonicalized provider discovery, and estimated spend for the DSH web UI · **installed v0.6.2** ✅

## Workflow

- [dsh-taskboard](https://github.com/LiuRJ99/dsh-taskboard-cloader) —— agent-first task board for the DSH web GUI: host-authoritative ledger with `taskboard_*` agent tools, project claim boundaries, per-task model execution in fresh sessions, host-side cron scheduling, optional git-worktree code isolation (dedicated task branches, commit evidence, one-click merge), live SSE kanban view, model initial SVG avatars for comments, lazy-loaded Mermaid diagrams, clickable file links, and optional sidebar tab integration with `dsh-better-sidebar` · **installed v0.6.0** ✅ — user's fork of [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) (synced from v0.5.1) with local modifications (local source dir `dsh-taskboard-cloader`)

  > **Local skew note:** the `dsh-better-sidebar` peer is declared `^0.16.1`
  > but **0.17.1** is installed, so it does not satisfy the caret range. It is
  > marked `optional: true` in `peerDependenciesMeta`, and the integration is
  > genuinely graceful: the tab module imports the sidebar package
  > **type-only**, resolves the live service through
  > `ctx.get('betterSidebar')`, and falls back to a legacy DOM mount when the
  > service or `registerTab` is absent. Only its runtime deps are `marked` and
  > `mermaid`.

## Web UI

- [dsh-better-sidebar](https://github.com/omdsh-dev/DSH-better-sidebar) —— a VSCode-like right sidebar for the DSH web GUI (explorer / editor / terminal / git / browser preview), session-isolated; supports pinned terminals and inline pinned tabs in TabBar; exposes service `ctx.betterSidebar` (`registerTab` / `registerFileViewer`) for other plugins to register sidebar tabs and file viewers · **installed v0.17.1** ✅ — resolved from npm (`^0.17.1`); there is **no** local source checkout, despite what earlier revisions of this list claimed. Ships CodeMirror 6 language packs (18 languages), `mermaid`, `node-pty`, `rxjs` and `ws`. Its optional peer `@huanlin/dsh-plugin-better-locale` is **not installed** (optional, so harmless).

## Security & Capability Control

- [dsh-tool-lazy-gate](https://github.com/LiuRJ99/dsh-tool-lazy-gate) —— session-lazy capability gate for DSH: hides and blocks high-privilege tool families until the user explicitly invokes the matching skill in the session; dual-layer defense with dynamic tool restriction (`tools.restrict`) and bypass-proof execution guard (`tools.guard`), suppressed gated prompt guidance sections, session-reconstruction on resume from durable `user/message` logs, and GUI-configurable capabilities in user settings (`tool-lazy-gate` namespace) · **installed v0.1.0** ✅ (local source dir `dsh-tool-lazy-gate`)

  The gate ships only **two** built-in fallback capabilities (`browser`,
  `computer`), but the local `~/.dsh/settings.yaml` configures a **third**:
  `taskboard` (`toolPrefixes: [taskboard_]`, `promptSections:
  [plugin:dsh-taskboard]`), so all three are gated here. `taskboard` is also
  special-cased at runtime: the plugin exposes a `grant-taskboard` RPC
  endpoint so loopback GUI code (e.g. the taskboard "create" panel) can grant
  just that capability to a validated live session — browser and computer
  grants stay host-side and are never client-controlled.

## Compatibility

- [dsh-sandbox-schema-shim](https://github.com/xiaohj233/dsh-compat-shims) —— removes redundant sandbox escalation fields from model-facing shell / file tool schemas in `danger-full-access` sessions · **installed v0.1.1** ✅ (package `dsh-sandbox-schema-shim` from the `dsh-compat-shims` monorepo). Fully standalone: **zero** dependencies and **zero** peer dependencies.

## Plugin Dependencies

Observed from the installed packages' `dependencies` / `peerDependencies` /
`peerDependenciesMeta` and source imports, resolved against host
`@deepseek-ai/dsh` **0.1.0-rc.8**.

The only true **plugin-to-plugin** edges — everything else is a host-provided
`@deepseek-ai/*` peer satisfied by the running DSH host:

```text
dsh-image-gen 0.4.1 ───(hard, satisfied)──▶ @LiuRJ99/dsh-cpa-plugin >=0.3.0 <0.5.0  (0.4.0 OK)
                                             imports '@LiuRJ99/dsh-cpa-plugin/image-generation'
                                            ▶ host peer: sharp ^0.35.4  (host has 0.35.3 — SKEW)

dsh-taskboard 0.6.0 ──(optional, SKEW)───▶ dsh-better-sidebar ^0.16.1  (0.17.1, out of range)
                                             optional: true; type-only import +
                                             ctx.get('betterSidebar') + legacy DOM fallback
                                            ▶ runtime deps: marked, mermaid

dsh-better-sidebar 0.17.1 ───────────────── service provider: exposes ctx.betterSidebar
                                             (registerTab / registerFileViewer)
                                            ▶ optional peer @huanlin/dsh-plugin-better-locale
                                               ^0.1.0 — NOT INSTALLED (optional, harmless)

dsh-tool-lazy-gate 0.1.0 ────────────────── capability gate: hooks tools.restrict + tools.guard
                                             + system prompt. Locally gates THREE families:
                                             browser_*, computer_use_*, taskboard_*
                                             (taskboard_ added via settings.yaml, not built-in)

@zibokapi/dsh-codex-computer-use 0.1.2 ──── resident Swift daemon + AX tree + SkyLight/CGEvent
                                             input; computer_use_* tools, approval policy,
                                             MCP server; gated by dsh-tool-lazy-gate
                                            ▶ bins: dsh-codex-computer-use, dsh-computer-mcp

@yuxianglin/dsh-bridge-browser 0.0.5 ────── WebSocket carrier + 20 browser_* tools; pairs with
                                             the Chrome/Firefox MV3 sidebar extension;
                                             gated by dsh-tool-lazy-gate until /browser
                                            ▶ deps: ws, schemastery

@LiuRJ99/dsh-cpa-plugin 0.4.0 ───────────── registers 'CLIProxyAPI' into llm-pi-ai
                                             (openai-responses, :8317, 21 models)
@LiuRJ99/dsh-workbuddy-provider 0.2.1 ───── registers 'WorkBuddy' into llm-pi-ai
                                             (openai-completions, :8318, 13 models)
                                            ▶ peers pinned 0.1.0-rc.6 vs host rc.8 — SKEW
                                            ▶ independent of each other

dsh-spend 0.6.2 ─────────────────────────── peers only: @deepseek-ai/cordis, dsh-credentials,
                                             dsh-home-paths, dsh-typert-protocol, schemastery
                                            ▶ zero runtime dependencies
dsh-sandbox-schema-shim 0.1.1 ───────────── zero dependencies, zero peer dependencies
```

### Peer-resolution audit

Checked programmatically (semver, `includePrerelease`) across all ten plugins:

| Plugin | Mandatory peers | Result |
| --- | --- | --- |
| `@zibokapi/dsh-codex-computer-use` 0.1.2 | 11 | all satisfied |
| `dsh-spend` 0.6.2 | 5 | all satisfied |
| `dsh-tool-lazy-gate` 0.1.0 | 5 | all satisfied |
| `dsh-sandbox-schema-shim` 0.1.1 | 0 | n/a |
| `dsh-image-gen` 0.4.1 | 19 | 1 skew (`sharp`) |
| `@LiuRJ99/dsh-cpa-plugin` 0.4.0 | 13 | 2 absent (`dsh-client-ui-primitives`, `dsh-client-ui-slots`) |
| `@LiuRJ99/dsh-workbuddy-provider` 0.2.1 | 6 | 4 skew (pins `rc.6`, host is `rc.8`) |
| `@yuxianglin/dsh-bridge-browser` 0.0.5 | 9 | 8 skew (`^0.1.1-rc.1` vs host `0.1.0-rc.8`) |
| `dsh-taskboard` 0.6.0 | 1 (optional) | 1 skew — optional and graceful |
| `dsh-better-sidebar` 0.17.1 | 17 | 3 absent (`dsh-client-ui-primitives`, `dsh-client-ui-slots`, `react-dom`) |

None of these are fatal. Every `@deepseek-ai/*` package resolves from the host at
**0.1.0-rc.8**, and `sharp` (0.35.3), `react` (18.3.1) and `react-dom` resolve
from the host's bundled `node_modules`. The `dsh-client-ui-*` packages are not
on disk under that exact name — they are satisfied through the
`dsh.client.inject` manifests each plugin declares in its `package.json`
(`cpa-plugin` injects 9, `image-gen` 8, `better-sidebar` 5), i.e. the host
composes them instead of npm-installing them. Read the warnings as "version pins
are stale relative to the host", not "broken install".

Key takeaways:

1. **`dsh-image-gen` 0.4.1 has the only hard plugin-to-plugin dependency**: `@LiuRJ99/dsh-cpa-plugin` ≥ 0.3.0 (<0.5.0) — install the CPA plugin first, otherwise image generation fails to resolve. Its `sharp` peer (`^0.35.4`) is one patch above the host's 0.35.3, but thumbnailing still works because `sharp` is host-provisioned.
2. **`dsh-cpa-plugin` (0.4.0) and `dsh-workbuddy-provider` (0.2.1) are sibling model-provider plugins** registered into the official `llm-pi-ai` provider registry (`CLIProxyAPI` on port 8317, `WorkBuddy` on port 8318). They are independent of each other, and WorkBuddy is the locally configured default agent provider.
3. **`dsh-better-sidebar` (0.17.1) is a service provider** — plugins may register sidebar tabs / file viewers through `ctx.betterSidebar`. `dsh-taskboard` 0.6.0 integrates with it optionally and degrades cleanly when the peer range is not met.
4. **`dsh-tool-lazy-gate` (0.1.0) provides session-lazy capability control** — locally it gates `browser_*`, `computer_use_*` **and** `taskboard_*`, even though only the first two are built-in defaults. The model cannot self-unlock via `skill()` tool calls; `taskboard_*` additionally has a GUI `grant-taskboard` RPC path.
5. **`@zibokapi/dsh-codex-computer-use` is a standalone desktop automation plugin** — macOS accessibility tree capture, screenshots, background input simulation, a resident Swift daemon (`dsh-computer-daemon.app`) and a standalone MCP server (`dsh-computer-mcp`).
6. **`@yuxianglin/dsh-bridge-browser` is a standalone bridge plugin** — 20 `browser_*` tools over a token-authenticated WebSocket, pairing with the Chrome/Firefox MV3 sidebar extension installed out-of-band via `scripts/install.sh`.
7. **`dsh-taskboard`, `dsh-spend` and `dsh-sandbox-schema-shim` are self-contained** with no mandatory inter-plugin dependencies.

## Install

```bash
# Add a plugin to the web profile
dsh plugin --profile web add <package-or-github-ref>

# Example: install from npm (prebuilt)
dsh plugin --profile web add dsh-taskboard

# Example: install tool lazy gate
dsh plugin --profile web add github:LiuRJ99/dsh-tool-lazy-gate

# Example: install computer-use plugin and setup daemon
dsh plugin --profile web add @zibokapi/dsh-codex-computer-use
npx @zibokapi/dsh-codex-computer-use

# Example: install from GitHub monorepo subpath
dsh plugin --profile web add "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
```

Restart `dsh web` and refresh the page after installing.

### How this local install is wired

Seven of the ten plugins are `link:`-ed local checkouts rather than registry
installs, which is why local edits take effect without republishing:

```jsonc
// ~/.dsh/profiles/web/package.json (abridged)
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

Order matters for exactly one pair: install `@LiuRJ99/dsh-cpa-plugin` **before**
`dsh-image-gen`, since the latter hard-depends on it.

## License

The listing itself is MIT. Each plugin keeps its own license — verified from the
installed `package.json` files: `dsh-taskboard` is **Apache-2.0**, the other
nine are **MIT**.