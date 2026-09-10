# Awesome DSH Plugins

The current DSH Web plugin catalog and the installation standard for this profile.
This file describes the present installation contract only. It is not a changelog,
incident report, or archive of previous installation attempts.

[简体中文](README.zh-CN.md)

## Current baseline

- DSH Host: `0.1.2-rc.1`
- Node.js: `24.19.0` on the verified machine
- Formal Web profile: `/Users/liurenjie/.dsh/profiles/web`, 13 plugins
- Profile pnpm: `11.7.0` (store `v11`) — see "pnpm pinning" below
- Install profile plugins with `dsh plugin --profile <profile> add ...`.
- Use a candidate profile before changing the formal `web` profile.
- Never copy another machine's profile, lockfile, `node_modules`, or absolute
  paths. Three entries do carry machine-local paths by necessity; they are listed
  under "Current profile exceptions".

### pnpm pinning

`dsh plugin` shells out to whatever `pnpm` resolves on `PATH`, so the pnpm that
runs must agree with the store that built the profile's `node_modules`.

The formal `web` profile was installed by pnpm `11.7.0` (store
`~/Library/pnpm/store/v11`). A plain pnpm `10.6.4` (store `v10`) refuses to
operate on it:

```text
ERR_PNPM_UNEXPECTED_STORE
```

Put the pinned pnpm first on `PATH` before running any `dsh plugin` command:

```bash
export PATH="/Volumes/S790C/work/liurenjie/work/dsh-work/.pnpm-11-shim:$PATH"
pnpm --version   # must print 11.7.0
```

Under pnpm 11 the `pnpm` field in a profile's `package.json` is no longer read.
Build-script approval lives in `pnpm-workspace.yaml` under `allowBuilds`, which
currently allows `@google/genai`, `node-pty` and `protobufjs`.

## Source rules

Three rules decide where a plugin may be installed from.

### 1. Self-forked projects are installed from the repository

Every project maintained as a fork under `LiuRJ99` is installed from a pinned Git
release tag on that repository, never from a registry package name.

### 2. Local development is published before it is installed

Work done in a local checkout must be committed, tagged, pushed and released, and
the profile must then install that published tag. A hand-edited file inside a
profile's `node_modules` is not an installation: it has no source in version
control, `package.json` no longer describes it, so no other machine can reproduce
it and the next reinstall silently reverts it.

### 3. Upstream updates are assessed for host compatibility first

An upstream release is adopted only after checking that it still supports the
current DSH Host. See "Upstream adaptation" below.

### Installing from a Git host

Two repository layouts decide whether `github:` installation is possible at all,
because pnpm does not run a git-hosted dependency's build unless the consumer
allowlists it:

- repositories that **commit their build output** (`lib/`) install directly with
  `github:<owner>/<repo>#<tag>`;
- repositories that **gitignore their build output** cannot be installed that way
  at all — the package would arrive with `main` pointing at a file that is not in
  the snapshot. They need the release tarball or the repository installer, and
  both are documented exceptions below.

### Source identity: npm name collisions

Four of these plugins share a name with an unrelated package on the public
registry. Installing by bare name silently installs **upstream instead of the
fork** — same name, different project, and the local enhancements are gone:

| Bare name | Resolves to | Authoritative source |
| --- | --- | --- |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `LiuRJ99/dsh-taskboard-cloader` |
| `dsh-spend` | `nonewind/dsh-spend` | `LiuRJ99/dsh-spend` |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `LiuRJ99/dsh-image-gen` |
| `dsh-github-mcp` | `ZIye1208/dsh-github-mcp` | `GitRuozhi/dsh-github-mcp` |

Always install these by their pinned Git source, never by name. `dsh-browser` and
`dsh-image-gen` also warn about this in their own READMEs.

## Current plugin catalog

| Plugin | Capability | Current delivery | Prerequisites / dependency |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI model provider, account/quota UI, speed modes, image-generation service | Git release `v0.4.1` | DSH peer services; CPA endpoint and credentials are configured by the user |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | WorkBuddy local model provider | Private Git release `v0.2.3` over SSH | GitHub SSH access; WorkBuddy local service on port `8318` |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | Browser bridge tools and Chrome/Firefox extension integration | Browser release `v0.1.5` via the repository installer; local bridge link is intentional | Node/pnpm, Chrome or Firefox; bridge and extension are one product |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS app state, accessibility tree, screenshots, mouse/keyboard input, MCP server | Git release `v0.1.3` | macOS, Xcode Command Line Tools, a rebuilt native daemon, Accessibility and Screen Recording grants |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web sidebar, explorer, editor, terminal, Git and browser surfaces; `ctx.betterSidebar` service | Exact registry version `0.18.0` | Optional service for Taskboard and ImageGen; `0.19.0` requires a newer host |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | Official GitHub MCP server bridge (`mcp__github__*`) plus a REST file reader | Git commit `fb03257c4c0dcfe4fa97c1c693d4eacd9184127c` (upstream publishes no tags) | `GITHUB_TOKEN` in `$DSH_HOME/.env` |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA-backed image generation, model catalog, image editing, Gallery and workspace save | Release `v0.5.0` tarball asset, staged to a persistent local file before `dsh plugin add` | Install CPA first; the repository gitignores `lib/`, so installing it from Git ships no entry point |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | Protected mobile access to DSH sessions | Exact registry version `0.3.12` | Web Host and the mobile patch; LAN access is enabled, the remote path is installed but off |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` tools and the `open-record-replay` skill for recording a demonstrated desktop workflow | Git release `v0.3.0` | macOS and Xcode Command Line Tools; an `open-record-replay` checkout, wired through the profile patch |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | Removes redundant sandbox fields from model-facing tool schemas | Git release tag `sandbox-schema-shim-v0.1.1`, package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token usage, statistics, billing-plan detection and spend views | Git release `v0.6.4` | DSH session, credentials and Web UI peer services |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host-authoritative tasks, task tools, workspace claims, scheduling and kanban UI | Git release `v0.6.5` | Optional Better Sidebar integration; advertises capability metadata to Lazy Gate |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | Session-scoped gating for the browser, computer-use, Taskboard and recorder tool families | Git release `v0.1.1` | Uses Taskboard and Record/Replay capability metadata when available |

DSH base and Web Host bundles are host layers, not community plugin entries in
this catalog.

## Dependency map

```text
DSH base + DSH Web Host
├─ Better Sidebar ── optional UI service ──┬─ Taskboard
│                                         └─ ImageGen
├─ CPA Provider ── required runtime service ── ImageGen
├─ Taskboard ── capability metadata contract ── Lazy Gate
├─ Record/Replay ── capability metadata contract ── Lazy Gate
├─ Record/Replay ── invokes bin/orr.js ── open-record-replay checkout
├─ GitHub MCP ── reads GITHUB_TOKEN ── $DSH_HOME/.env
├─ Browser bridge ↔ Chrome/Firefox extension
└─ Computer Use JS bundle ↔ macOS native daemon + TCC permissions
```

Lazy Gate gates four families, each unlocked only by a user-typed skill
invocation: `browser`, `computer`, `taskboard` and `recorder`. The `recorder`
family gates `orr_*`, which captures typed text verbatim and therefore must never
be reachable on the model's own initiative.

Independent plugins:

- WorkBuddy provider
- Spend
- Mobile
- Sandbox schema shim

## Installation modes

### Stable or cross-machine installation

Use a candidate profile first. Every source must be exact:

- registry package with an exact version;
- protected Git release tag or an exact commit when a repository publishes no tags;
- verified release tarball built from a fixed source commit.

Do not use `latest`, `main`, an unpinned branch, or another machine's `link:`.

### Local development

A `link:` is allowed only in a machine-local `web-dev` profile after the source
checkout has been built and its runtime entries have been checked. It is not a
portable installation format and it does not run the target package's build.

Once the work is worth keeping, follow rule 2 above: publish it, then install the
published tag.

### Special products

- **Browser:** do not use a plain `dsh plugin add` for the bridge package. Check out
  Browser release `v0.1.5` and run `scripts/install.sh` (or the Windows installer).
  It builds the bridge, registers the local bridge, builds the extension and copies
  it to the DSH-managed extension directory.
- **ImageGen:** build CPA first, then build ImageGen and create or download the
  `v0.5.0` release tarball. Stage it at a persistent local path before installing;
  do not let pnpm save GitHub's temporary signed redirect URL.
- **Computer Use:** install the `v0.1.3` release tag, then rebuild the native daemon
  with its setup CLI and grant Accessibility / Screen Recording separately.
- **WorkBuddy:** use the private `v0.2.3` SSH release tag and ensure the DSH/pnpm
  process can authenticate to GitHub over SSH.
- **Record/Replay:** install the `v0.3.0` release tag, then point the profile patch
  at an `open-record-replay` checkout.

## Installation procedure

The following order is the current Web profile order. Replace placeholders with
artifacts prepared for the target machine.

```bash
# Pin the pnpm that matches the target profile's store.
export PATH="/Volumes/S790C/work/liurenjie/work/dsh-work/.pnpm-11-shim:$PATH"

# Optional: initialize a disposable candidate profile with the Web Host.
dsh plugin --profile web-candidate add @deepseek-ai/dsh-web-app@0.1.2-rc.1

# Providers first.
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-cpa-plugin#v0.4.1"
dsh plugin --profile web-candidate add \
  "git+ssh://git@github.com:LiuRJ99/dsh-workbuddy-provider.git#v0.2.3"

# Download the v0.5.0 release asset to a persistent local path first.
dsh plugin --profile web-candidate add /path/to/dsh-image-gen-0.5.0.tgz

# Other release tags or exact registry versions.
dsh plugin --profile web-candidate add \
  "git+https://github.com/LiuRJ99/dsh-computer-use.git#v0.1.3"
dsh plugin --profile web-candidate add dsh-better-sidebar@0.18.0
dsh plugin --profile web-candidate add dsh-mobile@0.3.12
dsh plugin --profile web-candidate add \
  "github:GitRuozhi/dsh-github-mcp#fb03257c4c0dcfe4fa97c1c693d4eacd9184127c"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-record-replay#v0.3.0"
dsh plugin --profile web-candidate add \
  "github:xiaohj233/dsh-compat-shims#sandbox-schema-shim-v0.1.1&path:/packages/sandbox-schema-shim"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-spend#v0.6.4"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-taskboard-cloader#v0.6.5"
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-tool-lazy-gate#v0.1.1"

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

## Upstream adaptation

Each fork tracks an upstream project. Before adopting an upstream release, check
its host requirement against the current DSH Host, then decide deliberately.

| Fork | Upstream | Upstream latest | Installed | Assessment |
| --- | --- | --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | no releases | `v0.4.1` | Upstream is 1 commit ahead and `0` of them functional; nothing to adopt |
| `dsh-spend` | `nonewind/dsh-spend` | `v0.6.3` | `v0.6.4` | Fork leads upstream; nothing to adopt |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | no releases | `v0.1.3` | Fork leads upstream; nothing to adopt |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | no releases | `v0.3.0` | Fork leads upstream; nothing to adopt |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `v0.6.6` | `v0.6.5` | Candidate: 2 functional commits (DoD checklist ids, Windows caption layout). Upstream still declares `0.1.2-rc.1` compatible and adds no peer change, so it can be rebased — but the fork's own `dsh` compatibility range and its Better Sidebar layout fix must be re-applied, not dropped |
| `dsh-browser` | `Lum1104/dsh-browser` | `v0.1.3` | `v0.1.5` | Candidate: upstream has ~45 functional commits (reconnect ownership, Windows-portable bridge build). The fork already exceeds upstream's latest tag, so this is a merge, not a version bump |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `v0.5.1` | `v0.5.0` | Candidate: ~31 functional commits (BYOK providers, xAI/GLM image models, media-type detection). Upstream relaxed its peers to `>=4.0.0 <5` / `>=3.18.0 <4` while this fork pins exact host versions, so the merge must re-align the peer contract |

Rules:

- A higher upstream version is not by itself a reason to upgrade.
- A release that requires a newer host is not adoptable at all until the host moves.
- Re-verify the fork's own enhancements after any merge: the fork adds
  compatibility declarations and integration fixes that upstream does not have.

## Verification and update rules

A plugin update is complete only when all of these pass:

```bash
dsh --profile web-candidate --dump-config
```

- the package source is the expected release tag/exact version/release tarball;
- `main`, `exports`, `bin` and `dsh.bundle.patch` exist in the installed material;
- a newly added runtime dependency resolves from inside the installed package;
- required provider services are installed before consumers;
- the installed versions are the ones the catalog states;
- no unexpected machine-local path remains outside the documented exceptions;
- Web Host loads without a pending plugin entry;
- browser extension/native setup is completed for those special products;
- the formal profile is changed only after the candidate passes.

After changing the formal profile, restart DSH manually. Do not copy its
`package.json`, lockfile or `node_modules` to another machine.

## Current profile exceptions

### Machine-local paths

Three entries intentionally carry machine-local paths. All three are consequences
of a documented special product, not accidents:

| Entry | Value kind | Why |
| --- | --- | --- |
| `@yuxianglin/dsh-bridge-browser` | `link:` | The installer builds the bridge and the extension together, then registers the built bridge |
| `dsh-image-gen` | `file:` | The repository gitignores `lib/`, so only the release tarball carries a runnable package |
| `record-replay.repoRoot` | absolute path | The plugin invokes `bin/orr.js` from a local `open-record-replay` checkout |

A cross-machine install must re-create each of these from its own materials; none
of them is copyable.

### Browser bridge link

`@yuxianglin/dsh-bridge-browser` intentionally remains a local bridge link after
installing Browser release `v0.1.5`, because the repository installer must build
and register the bridge together with its extension.

The link resolves into the installer's **managed tree** at
`$DSH_HOME/dsh-browser` — a downloaded, marker-managed copy that is **not** a git
checkout — not into a developer checkout. The extension is built under
`~/.dsh/browser-extension`.

The installer downloads the `main` branch (`REMOTE_REF="main"` in
`scripts/install.sh`) rather than the documented release tag. On the verified
machine `main` was one `docs:` commit ahead of `v0.1.5`, so the built artifacts
were equivalent, but the installer is not tag-pinned. Treat the installer as a
trusted-input exception rather than a pinned source, and re-check this after any
browser install.

### Computer Use daemon

The native daemon lives at `$DSH_HOME/computer-use/dsh-computer-daemon.app`,
outside `node_modules`, so a plugin update does not rebuild it. After changing
the plugin version, rebuild the daemon with the package's own setup CLI:

```bash
node lib/setup.js --skip-permission-prompt   # build + install only
```

A plugin's `engines.node` floor can move when a dependency is added; re-check it
on upgrade even when the plugin's own version change looks small.

macOS keys each TCC grant on the helper's bundle id, code signature and on-disk
path. With the default ad-hoc signature, every rebuild changes the code hash and
macOS asks for Accessibility and Screen Recording again. Set
`DSH_COMPUTER_SIGN_IDENTITY` to a stable code-signing identity to keep grants
across rebuilds.

### Build-script approvals

pnpm `11.7.0` reads build-script approval from `pnpm-workspace.yaml`
(`allowBuilds`), not from the profile `package.json`, whose `pnpm` field it now
ignores. The profile currently allows `@google/genai`, `node-pty` and
`protobufjs`, so no install-hook warning is expected. A plugin repository's
workspace policy does not travel with the package; approve only exact scripts
after verifying their purpose.

## License

This catalog is MIT. Each plugin retains its own upstream license.
