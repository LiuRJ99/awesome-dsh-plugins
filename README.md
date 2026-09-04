# Awesome DSH Plugins

A compact inventory of the plugins currently enabled in the DSH Web profile.
The versions below were checked on 2026-09-04 against DSH `0.1.2-rc.1`.

[简体中文](README.zh-CN.md)

## Installed plugins

| Plugin | Version | Category | Maintainer / source |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | 0.4.0 | Model provider | LiuRJ99 fork of [dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider) |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | 0.2.1 | Model provider | LiuRJ99 fork |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 0.0.5 | Browser control | LiuRJ99 fork of [dsh-browser](https://github.com/Lum1104/dsh-browser) |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | 0.1.2 | Computer use | LiuRJ99 fork, adapted for DSH 0.1.2-rc.1 (local checkout) |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | 0.18.0 | Web UI | Author release; used without a local fork |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | 0.4.1 | Image generation | LiuRJ99 fork of [dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 0.3.8 | Mobile access | Community release by saya-ch |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 0.1.1 | Compatibility shim | Community monorepo package |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | 0.6.2 | Cost and usage | LiuRJ99 fork of [nonewind/dsh-spend](https://github.com/nonewind/dsh-spend) (`fix/startup-scan-performance`) |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | 0.6.4 | Workflow | LiuRJ99 fork of [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 0.1.0 | Security and capability control | LiuRJ99 fork |

The table reflects the active installation, not every plugin that exists in the
ecosystem. Personal forks and upstream packages are labelled separately so an
upgrade does not accidentally imply that an upstream project is being forked.

## Notable capabilities

- **Taskboard** — host-authoritative tasks and `taskboard_*` tools, workspace claim
  boundaries, per-task model execution, cron scheduling, optional git-worktree
  isolation, live kanban updates, clickable file links, model avatars, lazy
  Mermaid rendering, session-header actions with Cordis slot proxy deduplication,
  and optional Better Sidebar tabs. The fork uses upstream `0.6.4` as its compatibility
  base while retaining these locally developed integrations.
- **Lazy gate** — session-scoped gating for high-privilege tool families with
  dynamic restriction, execution guards, prompt suppression, and taskboard
  capability integration.
- **Better Sidebar** — the author's `0.18.0` right-sidebar release with explorer,
  editor, terminal, Git and browser surfaces, plus the `ctx.betterSidebar` service
  used by other plugins. This installation follows the author's package; it is not
  maintained as a local fork here.
- **Model and image providers** — CPA and WorkBuddy providers register model
  sources; CPA adds automatic Web UI quota/account polling and multi-window quota
  tracking; `dsh-image-gen` adds a CPA-backed image gallery and generation UI.
- **Browser, computer and mobile access** — the browser bridge exposes browser
  tools through the companion extension, Computer Use supplies macOS automation
  (adapted for macOS 13, local MCP and DSH rc.1), and DSH Mobile provides protected
  phone access to DSH sessions.
- **Utilities** — `dsh-spend` provides usage and cost views with background scan
  optimization to keep startup scans off the event loop, while the sandbox
  shim removes redundant sandbox fields from model-facing tool schemas.
- **Dynamic workflows (workspace)** — the workspace also develops
  [`@dsh-external/workflow`](https://github.com/omdsh-dev/dsh_workflow) (`dsh_workflow`),
  a dynamic workflow engine providing KodaX-parity multi-agent orchestration and persistence.

## Plugin relationships

The installed manifests declare one required plugin dependency and two optional
UI integrations:

- `dsh-image-gen` requires `@LiuRJ99/dsh-cpa-plugin` in the range `>=0.3.0 <0.5.0`.
- `dsh-taskboard` optionally integrates with `dsh-better-sidebar` `^0.18.0`.
- `dsh-image-gen` optionally integrates with the same Better Sidebar service.

The taskboard/lazy-gate connection is an integration contract, not an npm peer:
the taskboard advertises its capability metadata and the gate controls the
corresponding tools. The remaining plugins are independent or consume DSH-host
services.

## Compatibility

- Host baseline: DSH `0.1.2-rc.1`.
- `dsh-taskboard` declares `>=0.1.2-rc.1 <0.2.0` and marks `0.1.2-rc.1` as
  compatible in its manifest.
- `dsh-tool-lazy-gate`, the model providers, image generation, browser bridge,
  spend monitor and Computer Use packages use rc.1-compatible peer declarations.
- The author's Better Sidebar `0.18.0` release uses rc.1-compatible DSH peers.
- DSH Mobile `0.3.8` declares a compatible `0.1.2` release line through its peer
  ranges; the installed release was checked with the same host baseline.

Better Sidebar is intentionally consumed from the author's published release.
This repository does not patch, republish or otherwise maintain that project.

## Install

Install a published package with the DSH plugin command:

```bash
dsh plugin --profile web add dsh-better-sidebar@latest
dsh plugin --profile web add dsh-mobile@latest
```

Install one of the personal forks directly when you need the fork-specific
features:

```bash
dsh plugin --profile web add github:LiuRJ99/dsh-cpa-plugin
dsh plugin --profile web add github:LiuRJ99/dsh-workbuddy-provider
dsh plugin --profile web add github:LiuRJ99/dsh-browser#path:packages/browser/bridge-browser
dsh plugin --profile web add github:LiuRJ99/dsh-computer-use
dsh plugin --profile web add github:LiuRJ99/dsh-image-gen
dsh plugin --profile web add github:LiuRJ99/dsh-spend#fix/startup-scan-performance
dsh plugin --profile web add github:LiuRJ99/dsh-taskboard-cloader
dsh plugin --profile web add github:LiuRJ99/dsh-tool-lazy-gate
```

In a local development environment, plugins can also be linked directly from
workspace folders into the profile (`dsh plugin --profile web link <path>`).

Restart DSH after changing plugins. Install the CPA provider before
`dsh-image-gen`, because image generation depends on the provider's service.

## Maintenance boundary

The LiuRJ99 forked packages in this inventory are the packages whose local
features are intentionally retained: CPA (`dsh-cpa-plugin`), WorkBuddy
(`dsh-workbuddy-provider`), browser bridge (`dsh-browser`), Computer Use
(`dsh-computer-use`), image generation (`dsh-image-gen`), spend monitor
(`dsh-spend`), taskboard (`dsh-taskboard-cloader`) and lazy-gate
(`dsh-tool-lazy-gate`). `dsh-better-sidebar`, `dsh-mobile` and
`dsh-sandbox-schema-shim` are different: only upstream or author-published
packages are used, and no local maintenance is implied.

## License

This index is MIT. Each plugin keeps its own license: taskboard and DSH Mobile
are Apache-2.0; the other listed plugins are MIT unless their upstream project
states otherwise.
