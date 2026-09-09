# Awesome DSH Plugins

The current DSH Web plugin catalog and the installation standard for this profile.
This file describes the present installation contract only. It is not a changelog,
incident report, or archive of previous installation attempts.

[简体中文](README.zh-CN.md)

## Current baseline

- DSH Host: `0.1.2-rc.1`
- Node.js: `24.19.0` on the verified machine
- Formal Web profile pnpm: `10.6.4`
- Install profile plugins with `dsh plugin --profile <profile> add ...`.
- Use a candidate profile before changing the formal `web` profile.
- Never copy another machine's profile, lockfile, `node_modules`, or absolute paths.
- The formal profile currently has one intentional local-link exception:
  `@yuxianglin/dsh-bridge-browser`, because its official installer builds and
  registers a bridge together with a browser extension.

## Current plugin catalog

| Plugin | Capability | Current delivery | Prerequisites / dependency |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI model provider, account/quota UI, speed modes, image-generation service | Git release `v0.4.1` | DSH peer services; CPA endpoint and credentials are configured by the user |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | WorkBuddy local model provider | Private Git release `v0.2.1` over SSH | GitHub SSH access; WorkBuddy local service |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | Browser bridge tools and Chrome/Firefox extension integration | Browser release `v0.1.4` plus the repository installer; local bridge link is intentional | Clean browser checkout, Node/pnpm, Chrome or Firefox; bridge and extension are one product |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS app state, accessibility tree, screenshots, mouse/keyboard input, MCP server | Git release `v0.1.2` | macOS, Xcode Command Line Tools, Accessibility and Screen Recording permissions |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web sidebar, explorer, editor, terminal, Git and browser surfaces; `ctx.betterSidebar` service | Exact registry version `0.18.0` | Optional service for Taskboard and ImageGen |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA-backed image generation, model catalog, image editing, Gallery and workspace save | Release `v0.5.0` tarball asset, staged to a persistent local file before `dsh plugin add` | Install CPA first; source build requires a CPA sibling; do not install the source checkout directly from Git |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | Protected mobile access to DSH sessions | Exact registry version `0.3.12` | Web Host and the mobile patch; mobile access remains disabled until configured |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | Removes redundant sandbox fields from model-facing tool schemas | Git release tag `sandbox-schema-shim-v0.1.1`, package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token usage, statistics, billing-plan detection and spend views | Git release `v0.6.4` | DSH session, credentials and Web UI peer services |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host-authoritative tasks, task tools, workspace claims, scheduling and kanban UI | Git release `v0.6.5` | Optional Better Sidebar integration; advertises capability metadata to Lazy Gate |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | Session-scoped gating for browser and computer-use tool families | Git release `v0.1.0` | Uses Taskboard capability metadata when available |

DSH base and Web Host bundles are host layers, not community plugin entries in
this catalog.

## Dependency map

```text
DSH base + DSH Web Host
├─ Better Sidebar ── optional UI service ──┬─ Taskboard
│                                         └─ ImageGen
├─ CPA Provider ── required runtime service ── ImageGen
├─ Taskboard ── capability metadata contract ── Lazy Gate
├─ Browser bridge ↔ Chrome/Firefox extension
└─ Computer Use JS bundle ↔ macOS native daemon + TCC permissions
```

Independent plugins:

- WorkBuddy provider
- Spend
- Mobile
- Sandbox schema shim

## Installation modes

### Stable or cross-machine installation

Use a candidate profile first. Every source must be exact:

- registry package with an exact version;
- protected Git release tag;
- verified release tarball built from a fixed source commit.

Do not use `latest`, `main`, an unpinned branch, or another machine's `link:`.

### Local development

A `link:` is allowed only in a machine-local `web-dev` profile after the source
checkout has been built and its runtime entries have been checked. It is not a
portable installation format and it does not run the target package's build.

### Special products

- **Browser:** do not use a plain `dsh plugin add` for the bridge package. Check out
  Browser release `v0.1.4` and run `scripts/install.sh` (or the Windows installer).
  It builds the bridge, registers the local bridge, builds the extension and copies
  it to the DSH-managed extension directory.
- **ImageGen:** build CPA first, then build ImageGen and create or download the
  `v0.5.0` release tarball. Stage it at a persistent local path before installing;
  do not let pnpm save GitHub's temporary signed redirect URL.
- **Computer Use:** install the `v0.1.2` release tag, then run its setup CLI and
  grant Accessibility / Screen Recording separately.
- **WorkBuddy:** use the private `v0.2.1` SSH release tag and ensure the DSH/pnpm
  process can authenticate to GitHub over SSH.

## Installation procedure

The following order is the current Web profile order. Replace placeholders with
artifacts prepared for the target machine.

```bash
# Optional: initialize a disposable candidate profile with the Web Host.
dsh plugin --profile web-candidate add @deepseek-ai/dsh-web-app@0.1.2-rc.1

# Providers first.
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-cpa-plugin#v0.4.1"
dsh plugin --profile web-candidate add \
  "git+ssh://git@github.com/LiuRJ99/dsh-workbuddy-provider.git#v0.2.1"

# Download the v0.5.0 release asset to a persistent local path first.
dsh plugin --profile web-candidate add /path/to/dsh-image-gen-0.5.0.tgz

# Other release tags or exact registry versions.
dsh plugin --profile web-candidate add \
  "git+https://github.com/LiuRJ99/dsh-computer-use.git#v0.1.2"
dsh plugin --profile web-candidate add dsh-better-sidebar@0.18.0
dsh plugin --profile web-candidate add dsh-mobile@0.3.12
dsh plugin --profile web-candidate add \
  "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-spend#v0.6.4"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-taskboard-cloader#v0.6.5"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-tool-lazy-gate#v0.1.0"

# Inspect the composed profile before promoting it.
dsh --profile web-candidate --dump-config
```

For a formal profile, use the same verified materials with `--profile web`.
Install CPA before ImageGen. Do not manually edit `dsh.profile.bundles`; let DSH
reconcile bundles after each successful `dsh plugin add`.

## ImageGen source build contract

The source repository uses a sibling CPA checkout only during build:

```text
staging/
  dsh-cpa-plugin/
  dsh-image-gen/
```

```bash
cd staging/dsh-cpa-plugin
pnpm install --frozen-lockfile
pnpm run typecheck
pnpm run bundle

cd ../dsh-image-gen
PNPM_CONFIG_IGNORE_SCRIPTS=true pnpm install --frozen-lockfile
pnpm run typecheck
pnpm run test
pnpm run build
pnpm run pack:check
pnpm run pack:artifact -- --pack-destination /tmp/dsh-image-gen-artifacts
```

The resulting tarball is uploaded as the `v0.5.0` Release asset:

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.0/dsh-image-gen-0.5.0.tgz
```

It is the cross-machine material. Download it to a stable local path before
running `dsh plugin add`; GitHub redirects Release downloads through temporary
signed URLs, and those URLs must not be written into a long-lived profile
lockfile:

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.0/dsh-image-gen-0.5.0.tgz \
  -o /stable/path/dsh-image-gen-0.5.0.tgz
dsh plugin --profile web-candidate add /stable/path/dsh-image-gen-0.5.0.tgz
```

Do not copy the source checkout into a profile and do not hand-edit the tarball.

## Verification and update rules

A plugin update is complete only when all of these pass:

```bash
dsh --profile web-candidate --dump-config
```

- the package source is the expected release tag/exact version/release tarball;
- `main`, `exports`, `bin` and `dsh.bundle.patch` exist in the installed material;
- required provider services are installed before consumers;
- no unexpected machine-local `link:` remains;
- Web Host loads without a pending plugin entry;
- browser extension/native setup is completed for those special products;
- the formal profile is changed only after the candidate passes.

After changing the formal profile, restart DSH manually. Do not copy its
`package.json`, lockfile or `node_modules` to another machine.

## Current profile exceptions

- `@yuxianglin/dsh-bridge-browser` intentionally remains a local bridge link
  after installing Browser release `v0.1.4`, because the repository installer
  must build and register the bridge together with its extension. The browser
  repository checkout is clean and the extension is built under
  `~/.dsh/browser-extension`.
- The current profile emits a non-fatal warning for the old
  `dsh-mobile-remote-host` patch entry. It is a stale profile patch, not a
  dependency of the current `dsh-mobile` package.
- pnpm `10.6.4` may warn that `@google/genai` and `protobufjs` install hooks are
  ignored. The profile's build policy is separate from a plugin repository's
  workspace policy; approve only exact scripts after verifying their purpose.

## License

This catalog is MIT. Each plugin retains its own upstream license.
