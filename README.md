<p align="center">
  <img src="assets/logo.png" alt="Awesome DSH Plugins" width="160">
</p>

<h1 align="center">Awesome DSH Plugins</h1>

<p align="center">
  A curated catalog of community plugins for <a href="https://github.com/deepseek-ai/deepseek-harness">DeepSeek Harness</a> / DSH.
</p>

<p align="center">
  <a href="README.zh-CN.md">简体中文</a>
</p>

This catalog records what each public plugin does, the exact public source to
install, prerequisites, and any special delivery exception. Stable entries use a
pinned registry version, Git release/tag, exact commit, or release tarball.

It deliberately records no machine state. Concrete installed versions, local
paths and profile layout belong to whoever is running DSH, not to this catalog —
such details go stale on every install. To see what a given machine actually has,
inspect that machine:

```bash
dsh --profile <profile> --dump-config
```

Private repositories and machine-local integrations are intentionally omitted
from this public catalog. They cannot be a portable installation for every DSH
user.

## Catalog standard

A public entry is complete only when it answers five questions:

1. **What does it do?** State the capability in one sentence and link to the
   authoritative repository.
2. **Where does it come from?** Give an exact registry version, Git release/tag,
   commit, or release asset; call out any installer exception.
3. **What does it need?** List host, runtime, provider, credential, permission,
   and native-service prerequisites that affect installation or operation.
4. **What depends on it?** Show provider-before-consumer order and optional
   integrations without implying that optional peers are mandatory.
5. **How is it verified?** Give a reproducible check and disclose local paths,
   trust boundaries, remote exposure, or other security-sensitive side effects.

The catalog must not contain a machine's absolute paths, copied profile state,
private credentials, or a claim that a particular version is currently installed.

## Contents

- [Catalog standard](#catalog-standard)
- [Before you install anything](#before-you-install-anything)
- [Name collisions: install by source, not by name](#name-collisions-install-by-source-not-by-name)
- [Installing from a Git host](#installing-from-a-git-host)
- [Plugin catalog](#plugin-catalog)
- [Pinned install examples](#pinned-install-examples)
- [Dependency map](#dependency-map)
- [Desktop deployment](#desktop-deployment)
- [Candidate dependency overrides](#candidate-dependency-overrides)
- [Release and upstream checks](#release-and-upstream-checks)
- [Upstream relation](#upstream-relation)
- [Special products](#special-products)
- [Adapted plugin source builds](#adapted-plugin-source-builds)
- [macOS services outside the plugin directory](#macos-services-outside-the-plugin-directory)
- [Verification checklist](#verification-checklist)
- [License](#license)

## Before you install anything

1. **Pin every portable source.** Use a registry package at an exact version, a
   protected Git release/tag, an exact commit, or a release tarball with a
   checksum. Do not use `latest`, `main`, an unpinned branch, or another
   machine's `link:`. The Browser installer has a documented remote-bootstrap
   exception; its reproducible path is a local checkout of the exact tag, while
   the remote fallback is convenience-only and not a pinned install.
2. **Validate in a candidate profile first.** Add the plugin to a disposable
   profile and confirm DSH loads it before touching the profile you actually use.
3. **Install providers before their consumers.** A plugin that supplies a model
   or runtime service must be present before anything that depends on it.
4. **Do not hand-edit `dsh.profile.bundles`.** Let DSH reconcile the bundle list
   after each successful `dsh plugin add`.
5. **Do not hand-edit files inside a profile's `node_modules`.** Such an edit has
   no source in version control and is not described by `package.json`, so no
   other machine can reproduce it and the next reinstall silently reverts it. If
   a fix matters, it belongs in the plugin's repository — committed, tagged,
   released, and then installed as that published tag.
6. **Do not copy a `package.json`, lockfile or `node_modules` between machines.**
   Reinstall from pinned sources instead.
7. **Restart the formal profile after promotion.** A verified candidate does not
   change the already-running formal DSH process until it is restarted.
8. **Use the package manager appropriate to the target profile.** Desktop's
   installed CLI uses its bundled pnpm. The ordinary CLI resolves `pnpm` on
   `PATH`, and a profile's
   `node_modules` records the store that created it. A different pnpm major
   refuses to operate on it:

   ```text
   ERR_PNPM_UNEXPECTED_STORE
   ```

   If an install fails this way, put the pnpm version that built the profile
   first on `PATH` rather than re-installing the profile. Note also that under
   pnpm ≥ 11 build-script approval lives in `pnpm-workspace.yaml` (`allowBuilds`),
   not in the profile `package.json`, whose `pnpm` field is ignored.

### `link:` is for local development only

A `link:` dependency is acceptable only in a machine-local development profile,
after the source checkout has been built and its runtime entries have been
verified. A `link:` does not run the target package's build, and it is not a
portable installation format.

## Name collisions: install by source, not by name

Several plugins in this catalog share a name with an **unrelated project** on the
public npm registry. Installing by bare package name silently installs the wrong
project — same name, different code:

| Bare name | Resolves to | Authoritative source |
| --- | --- | --- |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `LiuRJ99/dsh-taskboard-cloader` |
| `dsh-spend` | `nonewind/dsh-spend` | `LiuRJ99/dsh-spend` |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `LiuRJ99/dsh-image-gen` |
| `dsh-github-mcp` | `ZIye1208/dsh-github-mcp` | `GitRuozhi/dsh-github-mcp` |

Never install these by bare name. Use the pinned source in the catalog table
below — a Git tag/commit for the Git-delivered entries, or the verified release
tarball for ImageGen — before typing any `dsh plugin add` command.

## Installing from a Git host

Whether `github:` installation works at all depends on the repository layout,
because pnpm does not run a git-hosted dependency's build scripts unless the
consumer allowlists them:

- A repository that **commits its build output** (`lib/`) installs directly with
  `github:<owner>/<repo>#<tag>`.
- A repository that **gitignores its build output** cannot: the package would
  arrive with `main` pointing at a file that is not in the snapshot. These need
  the release tarball or a repository-provided installer.

If a plugin reports a missing entry point after installing from a Git host, check
whether its `lib/` (or equivalent `main` target) is gitignored.

## Plugin catalog

| Plugin | Capability | Install from | Prerequisites |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI model provider, Codex Responses GPT routing, account/quota UI (including Kimi Code), speed modes, image-generation service | GitHub Release `v0.4.11` | DSH peer services; CPA endpoint and credentials configured by the user |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | Browser bridge tools and Chrome/Firefox extension integration | GitHub Release `v0.1.16-dev.2`: bridge tarball + Chrome extension zip; bridge `0.0.16-dev.2` | Matching bridge and Chrome extension assets; rebuild both only for source installation; Firefox needs a separate build and token setup |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS app state, accessibility tree, screenshots, mouse/keyboard input, MCP server | GitHub Release `v0.1.6-dev.4` | macOS, Xcode Command Line Tools, a rebuilt native daemon, Accessibility and Screen Recording grants |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web sidebar, explorer, editor, terminal, Git and browser surfaces; `ctx.betterSidebar` service | Exact registry version `0.28.2` | Optional UI service for Taskboard and ImageGen; `0.28.2` declares DSH `^0.2.1-rc.1` peers and carries the large-session cold-read/OOM fixes from `0.28.1`; Taskboard and ImageGen declare minimum-only optional peers (`>=0.21.1`), so newer Sidebar releases are accepted without the optional-peer warning — see the consumer compatibility notes below |
| [`dsh-decision-engine`](https://github.com/LiuRJ99/dsh-decision-engine) | Model-agnostic low-latency decision layer for DSH: pluggable Decision Engine and Providers, finite-candidate decision protocol, and Browser / Computer / Custom environment adapters | GitHub Release `v0.4.21` | DSH peer services; optional pinned SDK `@receptron/laya@0.1.1` for local inference; requires corresponding host tools/plugins if browser/computer adapters are enabled |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | Official GitHub MCP server bridge (`mcp__github__*`) plus a REST file reader | Exact Git commit `5be9077d46bfed66b76843bbcc7bdc459990b3af` (upstream publishes no tags) | `GITHUB_TOKEN` in the DSH process environment; DSH commonly loads it from `$DSH_HOME/.env` |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA-backed image generation, model catalog, image editing, Gallery and workspace save | GitHub Release `v0.5.14` tarball asset; SHA-256 `7eb68b4805cfb90cb0562bc39972f74a5153010ac1b5a9e0a1a579ea34040949` | Install CPA first; ImageGen declares minimum-only peers: CPA `>=0.4.0` and optional Better Sidebar `>=0.21.1`. The repository gitignores `lib/`, so a Git install ships no entry point |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | Access to DSH sessions from a mobile device | Exact registry version `0.6.1` | LAN access is separate from optional remote access; remote is off by default, paired devices are fully trusted, LAN uses a pinned local CA, and remote uses the provider's HTTPS endpoint. `0.6.1` requires Node `^22.19 || >=24`; validate the matching mobile client and pairing flow locally. Upstream `0.8.0` raises its React peer to `^19.3` and is not pinned here until verified against a matching Host build |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` tools and the `open-record-replay` skill for recording a demonstrated desktop workflow | GitHub Release `v0.3.3-dev.3` | macOS and Xcode Command Line Tools; exact fork [`open-record-replay`](https://github.com/LiuRJ99/open-record-replay) tag `v0.1.1`, wired through a profile patch |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | Removes redundant sandbox fields from model-facing tool schemas | Git tag `sandbox-schema-shim-v0.1.1`, package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token usage, statistics, billing-plan detection and spend views | GitHub Release `v0.6.7-dev.4` | DSH session, credentials and Web UI peer services |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host-authoritative tasks, task tools, workspace claims, scheduling and kanban UI | GitHub Release `v0.7.12` | Optional Better Sidebar integration (minimum-only peer `>=0.21.1`); publishes capability metadata to Lazy Gate |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | Session-scoped gating for browser and computer-use by default, plus configured Taskboard/recorder families | GitHub Release `v0.1.10` | Browser/computer are built-in defaults; Taskboard and Record/Replay require capability config plus the adapted skill metadata; includes the Web connection workaround |

### Compatibility note

The maintained forks use **forward compatibility by default**: `engines.dsh >=0.2.0-rc.2`, with matching DSH peer ranges. A newer Host does not require a new plugin release just to add its version to an allowlist. Keep the known minimum; narrow the range only for a confirmed incompatibility. Build SDK inputs and installation sources remain pinned independently.

Host admission checks `engines.dsh` first, otherwise `@deepseek-ai/dsh` and `@deepseek-ai/dsh-*` peers, with prereleases included. `dsh.compatibility` is supplementary metadata. Missing test evidence is not an unsupported-version declaration, and admission is not proof that every future Host has been tested. Third-party plugins retain their own declared ranges; Sidebar 0.28.2 uses `^0.2.1-rc.1`.

CPA 0.4.11 uses Host-provided peers instead of installing another settings/config-editor stack. Its connection patch and Lazy Gate 0.1.10 preserve webStartup and webServer without requiring the retired webRuntime service. Browser passes the Host-owned Peer to Gateway calls, streams and event replies; identity never comes from extension payloads. Computer Use no longer requires the retired invariants service.

Taskboard 0.7.12 and ImageGen 0.5.14 declare minimum-only peers: optional Better Sidebar `>=0.21.1`, and (for ImageGen) CPA `>=0.4.0`, with no upper bounds. Sidebar 0.28.2 and newer therefore stay accepted without an optional-peer warning; re-verify the Taskboard board tab and ImageGen Gallery after upgrading. The language-pack peer in Sidebar is optional. ImageGen was built with CPA 0.4.11 and declares CPA `>=0.4.0`; use its checksum-verified release tarball because Git omits lib.

These releases retain CPA-only image generation, Gallery transactions and workspace metadata, Taskboard durable FIFO and explicit session ownership, Browser session bindings and approvals, and explicit user unlock in Lazy Gate. Historical Desktop, model and native-permission checks below apply only to their stated versions.

### Candidate dependency overrides

Create an independent candidate profile and preserve build approvals and unrelated overrides. These targets no longer need the old CPA @deepseek-ai/dsh-* overrides. Remove those old Host pins so the official Host supplies services.

```yaml
nodeLinker: hoisted
autoInstallPeers: false
```

pnpm 11 build approvals belong under allowBuilds in pnpm-workspace.yaml; review packages individually without global approvals or version exemptions. Desktop uses its bundled package manager. Ordinary CLI installs need a pnpm major matching the profile store.

## Pinned install examples

Use a newly created `<candidate-profile>` for the first pass. The target profile
must already provide the official DSH Web Host bundle; it is not a community
plugin in this catalog. Use the candidate dependency configuration above.
Enable Computer Use only after its macOS native prerequisites are ready; skip that line elsewhere.
These commands use only public, exact sources:

```bash
# Providers first (protected release tag).
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-cpa-plugin#v0.4.11"
# Exact registry versions.
dsh plugin --profile <candidate-profile> add dsh-better-sidebar@0.28.2
# Optional: install only for mobile access; skip in Desktop-only deployments.
dsh plugin --profile <candidate-profile> add dsh-mobile@0.6.1

# Fixed sources; published forks use exact release tags.
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-decision-engine#v0.4.21"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-computer-use#v0.1.6-dev.4"
dsh plugin --profile <candidate-profile> add \
  "github:GitRuozhi/dsh-github-mcp#5be9077d46bfed66b76843bbcc7bdc459990b3af"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-record-replay#v0.3.3-dev.3"
dsh plugin --profile <candidate-profile> add \
  "github:xiaohj233/dsh-compat-shims#ba4088c1a7b77b1c73fd5d5438f46800720d6bcd&path:/packages/sandbox-schema-shim"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-spend#v0.6.7-dev.4"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-taskboard-cloader#v0.7.12"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-tool-lazy-gate#v0.1.10"

# Inspect the composed candidate before promoting it.
dsh --profile <candidate-profile> --dump-config
```

After the candidate passes, repeat the same exact sources with your formal
profile and restart DSH. Do not copy the candidate's `package.json`, lockfile, or
`node_modules` into another machine.

## Dependency map

```text
DSH base + DSH Web Host
├─ Better Sidebar ── optional UI service ──┬─ Taskboard
│                                         └─ ImageGen
├─ CPA Provider ── required runtime service ── ImageGen
├─ Taskboard ── capability metadata contract ── Lazy Gate
├─ Record/Replay ── capability metadata contract ── Lazy Gate
├─ Record/Replay ── invokes bin/orr.js ── forked open-record-replay checkout
├─ GitHub MCP ── reads GITHUB_TOKEN ── DSH process environment (often $DSH_HOME/.env)
├─ Browser bridge ↔ Chrome/Firefox extension
└─ Computer Use JS bundle ↔ macOS native daemon + TCC permissions
```

Lazy Gate enables the built-in `browser` and `computer` families by default.
`taskboard` and `recorder` can also be gated when the capability is enabled and
the adapted plugin publishes its skill metadata. Every gated family is unlocked
only by a user-typed skill invocation. The `recorder` family gates `orr_*`,
which captures typed text verbatim and **must never be reachable on the model's
own initiative**.

Independent plugins (no entry in the map above other than the host):

- Decision Engine
- Spend
- Mobile
- Sandbox schema shim

## Desktop deployment

Official Desktop uses its own Host and Plugin Manager. Installing into a CLI/Web profile does not install into Desktop.
Verify Desktop's actual Host, Home and profile before installing. Validate in an independent
candidate Home, preserve existing configuration and data, then promote and restart the formal Desktop.
Finder launches need a supported Home configuration; do not assume they inherit terminal exports.
Use Desktop's Plugin Manager and the pinned catalog sources, with providers installed first and old Host overrides removed.
The official app is sufficient as the entry point: Browser needs no extra launcher, while Computer Use still requires the helper referenced by its active provider.

Browser consists of a Desktop bridge and a separately loaded Chrome extension. Verify both versions
in `chrome://extensions`, reload after updating, and use the extension from the same fixed Release.
The bridge publishes Desktop's current WebSocket address on loopback discovery port `43189`.
Leave the extension bridge address empty and save to discover it automatically and reconnect after
Desktop changes its Web port. A manually entered address stays pinned; when needed, configure:

```text
ws://127.0.0.1:<actual Desktop Host port>/ext/bridge
```

Chrome loopback needs no manually entered token. Firefox and remote deployments still need their
documented authentication. A connected bridge does not grant control: session `/browser` gating
and extension approvals remain. Keep Computer Use's native helper and its TCC grants;
the same helper appears in both Accessibility and Screen Recording lists. Desktop's own permissions
do not replace the helper's permissions. Validate native preflight inside the actual Host.

Before migration, back up sessions, attachments, Taskboard, Gallery and configuration. Check titles,
historical-log decoding, task/workspace ownership and image resources after migration. Do not
copy `node_modules`, re-sign the official app or alter its bundled Host. Data is not automatically
synced between independent Web and Desktop Homes. Host restarts may end Sidebar terminals.
Desktop's internal HTTP/Browser bridge remains necessary when a standalone Web installation is removed.
Mobile is optional and is skipped in Desktop-only deployments.

### Desktop validation

This historical table describes the earlier tested combination, not the new catalog targets, the live state of a machine or a guarantee
for other credentials, platforms or model inputs. Classifications apply only to the stated scope.

| Classification | Capability / target | Evidence and limits |
| --- | --- | --- |
| Fully usable | CPA `0.4.8`, WorkBuddy `0.2.7` | Real text model replies; image generation/editing succeeded on retry, but one provider editing response contained only text |
| Fully usable | Browser `v0.1.13-dev.1` | 540 source tests; after user reload/approval, background open preserved foreground and subsequent input/click targeted the new background tab; Firefox untested |
| Fully usable | Taskboard `0.7.7` | 439 tests passed, 2 opt-in Git tests skipped; two scheduled tasks produced real model replies with one-second dispatch spacing |
| Fully usable | ImageGen `0.5.10`, Sidebar `0.24.1` | 148 ImageGen tests; Gallery/Taskboard surfaces and persisted images verified; IndexedDB failure/retry verified in source tests, not injected into the formal Gallery |
| Fully usable | Computer Use `0.1.6-dev.2` | Native build and real input/click after human approval; a candidate without native permission cannot be counted as passed |
| Fully usable | Record/Replay `0.3.3-dev.1` | Recording/replay confirmed by the user after manual `/open-record-replay`; agent must not unlock or initiate recording independently |
| Fully usable | Spend `0.6.7-dev.2` | Actual usage and UI verified; cost is an estimate |
| Fully usable | Lazy Gate `0.1.7` | Official Host's first-turn tools and same-turn user unlock verified; aggregate loaded state and accessible icon refresh replace duplicate per-card text |
| Fully usable, advice only | Decision Engine `0.4.19` | Explicit `executionMode: advice-only` exposes `decision_decide`, blocks actions and tasks, and omits `decision_run`; default remains `execute`, execution mode untested |
| Needs adjustment | Optional Laya SDK `0.1.1` | Inference works, but one of four constraint-quality samples failed; `0.1.2` not quality-tested, automatic execution not accepted |
| Needs adjustment | GitHub MCP `1.1.0` | REST `github_file_read` works; official Host fails to present MCP embedded-resource bodies to the model |
| Fully usable | Sandbox shim `0.1.1` | No schema changes under read-only/workspace-write; removes redundant fields only in an already-authorized danger-full-access session |
| Incompatible in part | Legacy log formats | Older subagent descriptors and `request/header.config.speed` may be rejected by the target Host; retain originals for a supported migration |
| Skipped | Mobile `0.6.1` | Optional client; pairing and remote access not certified by Desktop-only validation |

Remaining Host-supplied and prerelease peer warnings must be evaluated separately from actual
dependency resolution. Taskboard/ImageGen's former optional Sidebar warning was resolved by enumerated
ranges at the time; current releases declare minimum-only `>=0.21.1` (see the catalog targets
above). No version exemption is used. An empty historical session has no content from
which to infer a title, and orphan task ownership needs authoritative evidence before rebinding.

### 2026-10-15 candidate qualification

The catalog targets Browser `v0.1.14-dev.1`, Taskboard `v0.7.9`, ImageGen `v0.5.11`
and Sidebar `0.26.0` retain Host `0.2.0-rc.2`. Taskboard adds workspace-aware session
jumps, cross-process ledger serialization, live-run adoption after reload and partial refresh
recovery. Browser preserves manual history scrolling and offers a return-to-bottom button.
ImageGen synchronizes its settings snapshot immediately after subscribing and aligns its runtime version.

1,150 source tests passed (Taskboard 456, ImageGen 149, Browser 545), with successful
[Taskboard CI](https://github.com/LiuRJ99/dsh-taskboard-cloader/actions/runs/38689505661),
[ImageGen CI](https://github.com/LiuRJ99/dsh-image-gen/actions/runs/38688935380) and
[Browser CI](https://github.com/LiuRJ99/dsh-browser/actions/runs/38688941298).
Taskboard `v0.7.9` fixes the frozen-lockfile mismatch in superseded `v0.7.8`.
Published artifacts passed checksum, runtime-entry and dependency-resolution checks.
An isolated official Host loaded CPA, Sidebar, Taskboard, ImageGen and Browser as active,
with no pending community entries or bundle errors. Both Sidebar consumers typechecked
against the actual published `0.26.0` service declarations.

This qualification does not add new live model, native-permission or complete Desktop UI
coverage. Browser's Chrome manifest is `0.1.14`; its permissions are unchanged.
A formal installation needs a later Host restart and extension reload to use the new files.
Mobile remains optional and outside this qualification.

### 2026-10-18 Host compatibility qualification

The published targets use the official 0.2.1-rc.1 SDK. Public-plugin source checks
passed 1,826 tests: CPA 103, Computer Use 153, Decision Engine 328, ImageGen 149,
Record/Replay 26, Spend 26, Taskboard 456, Lazy Gate 39, Browser bridge 134 and
Chrome extension 412. Type checks and package-entry checks passed for the changed
packages. The release assets for that qualification were downloaded and checksum-verified.

An isolated signed Desktop validated startup, settings read/write/restoration,
Gateway Peer calls and forwarded-event streaming. The native helper built on macOS;
its permissions still require checks in each deployment. This round does not add
live model billing, recording/replay, Firefox or Mobile coverage.

### 2026-10-19 forward compatibility qualification

The published targets keep the official `0.2.1-rc.1` build SDK and admit newer Hosts by default. Public source checks passed 1,844 tests: CPA 105, Computer Use 155, Decision Engine 330, ImageGen 151, Record/Replay 28, Spend 28, Taskboard 458, Lazy Gate 41, Browser bridge 136 and Chrome extension 412. Applicable type checks, builds and package-contract checks passed. Regression tests cover newer prereleases without adding an RC allowlist; they do not simulate those future runtimes.

Published assets were downloaded and matched their SHA-256 checksums. An isolated official `0.2.1-rc.2` Host loaded all 16 components from 13 community plugins as active, with no community pending entries or loader errors. Sources, runtime entries and dependencies passed inspection. This adds startup coverage only; live model billing, recording/replay, Firefox and Mobile were not retested.

## Release and upstream checks

The public forks maintained in this round have GitHub Releases and v* tags protected against updates and deletion. Record/Replay uses v0.3.3-dev.3. GitHub MCP and Sandbox shim retain pinned sources. Source tags, npm packages and release assets are distinct delivery forms; verify provenance, actual entry points and checksums individually.

Computer Use's source CI passed; its npm publish job failed with
[ENEEDAUTH](https://github.com/LiuRJ99/dsh-computer-use/actions/runs/39191230437).
The catalog installs its protected Git release tag and does not depend on npm publication.

```bash
gh release view <catalog-tag> --repo <owner/repo> \
  --json tagName,isDraft,isPrerelease,publishedAt,assets,url
gh api repos/<owner/repo>/git/ref/tags/<catalog-tag>
# For an annotated tag, dereference git/tags/<object.sha> to its commit.
npm view <registry-package>@<exact-version> version dist.integrity
```

Use these reviewed identities to check the catalog sources; they are not an installation inventory:

| Source | Target | Commit |
| --- | --- | --- |
| CPA | `v0.4.11` | `305896e8da2b5fa450728e5a24e8e877e5712f46` |
| Browser | `v0.1.16-dev.2` | `256733493119dca239ed28b5f19aaaae129da7c7` |
| Computer Use | `v0.1.6-dev.4` | `f349b40aebe221b019aed4a39bb8613f6ef1373b` |
| Decision Engine | `v0.4.21` | `810f1c43a2de4c98377967bf2cc04bd33d0a9b46` |
| GitHub MCP | Exact commit | `5be9077d46bfed66b76843bbcc7bdc459990b3af` |
| ImageGen | `v0.5.14` | `bde3a303d09278b1c3474129bfe17db0cdaf791d` |
| Record/Replay | `v0.3.3-dev.3` | `f1e2dc1f8f4fc29e20edce6036740688494ee553` |
| Sandbox shim | `sandbox-schema-shim-v0.1.1` | `ba4088c1a7b77b1c73fd5d5438f46800720d6bcd` |
| Spend | `v0.6.7-dev.4` | `c15b0565848493c1b02a2e7cbfe6151de4e6cc5d` |
| Taskboard | `v0.7.12` | `776aae96af786c57eeb16c9e6f822d57320962ac` |
| Lazy Gate | `v0.1.10` | `4f1bb51fa9a48c578a8839a00e3b6170c66b962a` |
| Recorder helper | `v0.1.1` | `91188499023cbce9f56c11b58f71bc7e8298a33d` |

| Release asset | SHA-256 |
| --- | --- |
| [Browser bridge tarball](https://github.com/LiuRJ99/dsh-browser/releases/download/v0.1.16-dev.2/yuxianglin-dsh-bridge-browser-0.0.16-dev.2.tgz) | `adc797a216ecbd4ded3e430c6716c12483bc4d084278fd0d9f04aa780cc32be3` |
| [Chrome extension zip](https://github.com/LiuRJ99/dsh-browser/releases/download/v0.1.16-dev.2/dsh-browser-extension-0.1.16-dev.2.zip) | `09b105aaefca050cf57b59456129a1101c36bdbf8632fc29612690b6de687d11` |
| [ImageGen tarball](https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.14/dsh-image-gen-0.5.14.tgz) | `7eb68b4805cfb90cb0562bc39972f74a5153010ac1b5a9e0a1a579ea34040949` |

For forks, start with an open `sync/upstream-main` PR on the default branch. Read the complete
body, commits, files, reviews and decision comments before any local upstream fetch/diff/merge.
The PR is a frozen snapshot; do not refresh it when upstream moves. A successful workflow proves
detection, not adoption. With no open PR, inspect the workflow and previous evaluation boundary first.

```bash
gh pr list --repo <fork-owner/repo> --base <default-branch> \
  --head sync/upstream-main --state open
gh pr view <number> --repo <fork-owner/repo> --json body,commits,files,comments,reviews
gh api --paginate repos/<fork-owner/repo>/pulls/<number>/files
gh run list --repo <fork-owner/repo> --workflow sync-upstream.yml --limit 3
```

Each fork's daily sync workflow must preserve an open snapshot and the last evaluated upstream SHA.
Review source/config conflicts separately from generated outputs and lockfiles. Selective adoption or
rejection is recorded on the closed PR as `partial` or `rejected`; whole adoption as `whole`.
Changes after the recorded boundary belong to the next PR. No automatic merge or `-X ours` shortcut.
For self-maintained repositories, compare the release source with main and distinguish runtime changes
from documentation/workflows before deciding whether a new package release is needed.

| Sync PR (closed as `partial`) | Adopted in the published fork | Deferred / preserved |
| --- | --- | --- |
| [Browser #5](https://github.com/LiuRJ99/dsh-browser/pull/5) | `6d6ce252`: background open, approval label, separate foreground/control targets; `v0.1.13-dev.1` | Preserve DSH 0.2 peers, per-session bindings and permission restrictions |
| [Taskboard #5](https://github.com/LiuRJ99/dsh-taskboard-cloader/pull/5) | `930bb2c6` / `5746284b`: durable queue, atomic handoff, serialized dispatch and cancellable waits; `v0.7.7` | No automatic successors, runAt redesign or bulk purge; preserve recurring review cards and Sidebar integration |
| [ImageGen #5](https://github.com/LiuRJ99/dsh-image-gen/pull/5) | `d9a58cd9`: save-result propagation and persistence-only UI retry; `v0.5.10` | Preserve CPA, transactions, tombstones, favorites and workspace metadata; defer studio/OAuth/batch ZIP |
| [Browser #6](https://github.com/LiuRJ99/dsh-browser/pull/6) | `afa360b` / `5513239`, panel portion of `aff85fb`: follow only near the bottom and resume button; `v0.1.14-dev.1` | Preserve canonical Gateway, session bindings and permission gates; defer upstream remote-host/model-selection restructuring |
| [Taskboard #6](https://github.com/LiuRJ99/dsh-taskboard-cloader/pull/6) | `e485fc8` / `fd3959e` / `0b51895` / `5ca3c54`: workspace jump, ledger serialization, live-run reload adoption and refresh recovery; `v0.7.9` | Preserve model/speed routing, durable FIFO, multi-repo and gate integration; remaining snapshot decisions are recorded on the PR |
| [ImageGen #6](https://github.com/LiuRJ99/dsh-image-gen/pull/6) | Settings subscription synchronization from `585d4e9`; `v0.5.11` | Preserve CPA-only providers, Gallery transactions and workspace metadata; MIME-adapter changes do not apply to this fork; defer studio/OAuth |

## Upstream relation

Several catalog entries are forks. A fork can carry compatibility declarations
and integration fixes that upstream does not have, so a higher upstream version
is **not by itself** a reason to upgrade — and a merge is not a version bump.
Assess each upstream release against the host you actually run before adopting it.

| Fork | Upstream | Notes |
| --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | Query the fork sync PR first; check provider contracts and official dependency resolution |
| `dsh-spend` | `nonewind/dsh-spend` | The fork preserves explicit DSH compatibility and UI fixes; query upstream versions from their public sources |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | The fork preserves Host peers, native integration and security fixes; JS upgrades also require helper and permission checks |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | The fork preserves target Host types and gate integration, and depends on the exact `v0.1.1` tag of [`LiuRJ99/open-record-replay`](https://github.com/LiuRJ99/open-record-replay) for the recorder CLI |
| `dsh-taskboard` | `cloader/dsh-taskboard` | Preserve recurring session reuse, Sidebar integration, attachments, data-directory migration, multi-repo permissions, durable FIFO and scheduling enhancements. Reviewed PR #7 and upstream through `bf9f35eee24893ac9f4ac10e6783780e0aec9387`; this compatibility release defers ZenTao/TAPD and unrelated UI expansion, and preserves explicit errors when a selected session is unavailable. The evaluation PR remains open |
| `dsh-browser` | `Lum1104/dsh-browser` | Historical fork tag `v0.1.11` includes the fork installer and Host fixes plus rich-text input, bridge-restart resume, dependency-security fixes, prioritized controls for visible dialogs, and opt-in scoped discovery of non-semantic controls |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | Preserve CPA and Sidebar integration. Reviewed PR #7 and upstream through `b128d0d38416360f6835c07331bc5b78ddcd242a`; this compatibility release defers Google subscription/studio, non-CPA transports and unrelated output/watermark features. The evaluation PR remains open |
| `open-record-replay` (helper CLI) | `humblebanana/open-record-replay` | The fork fixes foreign-cwd CLI behavior and native build targets; it is not a separate DSH plugin |

Query open `sync/upstream-main` PRs first. Review their full bodies, commits, files and decision comments before local fetch/diff or trial merges.
A successful sync workflow does not prove upstream was absorbed. See [release and upstream checks](#release-and-upstream-checks).

Rules:

- A higher upstream version is not by itself a reason to upgrade.
- A release that requires a newer host is not adoptable at all until the host moves.
- After any merge, re-verify the fork's own enhancements.
- A fix the fork carried can later appear upstream. Re-check the delta before
  assuming a fork-only patch still has to be reapplied.

## Special products

These need more than a `dsh plugin add`.

- **Browser** — both bridge and browser extension must be ready. Check out exact commit `256733493119dca239ed28b5f19aaaae129da7c7`,
  install with a frozen lockfile, build the workspace, pack the bridge and add it to a candidate profile; see [source builds](#adapted-plugin-source-builds).
  Load Chrome's `extensions/dsh-browser/dist/`. The release extension manifest is `0.1.16`; verify the loaded extension as well as bridge `0.0.16-dev.2`.
  `scripts/install.sh` modifies the `web` profile by default; use the explicit build/pack path for candidate validation.
  Its remote convenience fallback downloads unpinned `main`. Firefox needs a separate `pnpm --filter dsh-browser-extension run build:firefox` build and token setup and was not tested in this round.
- **ImageGen** — to reproduce its build, build CPA from its exact `v0.4.11` tag first, then ImageGen from
  its exact `v0.5.14` tag, and use the published `v0.5.14` release tarball. Its
  asset SHA-256 is
  `7eb68b4805cfb90cb0562bc39972f74a5153010ac1b5a9e0a1a579ea34040949`.
  Download it to a stable local path before `dsh plugin add`; GitHub serves
  Release downloads through temporary signed redirect URLs, and those must not end
  up in a long-lived lockfile. Do not copy the source checkout into a profile or
  hand-edit the tarball.
- **Computer Use** — install `0.1.6-dev.4` from the exact release tag above, then rebuild the native
  daemon with the package's setup CLI and grant Accessibility / Screen Recording
  separately.
- **Record/Replay** — install `0.3.3-dev.3` from its protected release tag above, then point the profile
  patch's `repoRoot` or `cliPath` at the exact `v0.1.1` tag of the
  [forked recorder](https://github.com/LiuRJ99/open-record-replay). Build that
  checkout before pointing DSH at it:
  ```bash
  git clone --branch v0.1.1 --depth 1 \
    https://github.com/LiuRJ99/open-record-replay.git
  cd open-record-replay
  npm install
  npm run build:native
  ```
  The shipped
  patch leaves that path unset intentionally; use `ORR_REPO_ROOT` or
  `ORR_CLI_PATH`, or overlay a profile patch. This fork is required because the
  plugin runs the CLI with the session workspace as its working directory while
  the upstream recorder resolves its Swift package against `process.cwd()`, so
  recorder-backed permission/start calls fail with `chdir error: No such file or
  directory (2)` and the recording workflow is unusable; quality commands also
  fail from a foreign working directory. The fork also lowers the native
  recorder's deployment target to macOS 13 /
  Swift 5.9.

### Adapted plugin source builds

Inspect existing checkout changes first; use `git pull --ff-only` on an existing main checkout
or clone a missing repository. For reproduction, use a separate checkout at the reviewed
tag/commit above. Do not reset, clean or discard work. Read applicable repository `AGENTS.md`.

The following creates an isolated **CLI source-test runtime**, not a Desktop installation.
Use Node 24, retain existing Homes, and pin the complete official npm Host:

```bash
export DSH_VALIDATION_ROOT="$PWD/.dsh-validation"
mkdir -p "$DSH_VALIDATION_ROOT/runtime" "$DSH_VALIDATION_ROOT/artifacts"
npm install --prefix "$DSH_VALIDATION_ROOT/runtime" --save-exact \
  @deepseek-ai/dsh@0.2.1-rc.1 pnpm@11.7.0
export PATH="$DSH_VALIDATION_ROOT/runtime/node_modules/.bin:$PATH"
export DSH_HOME="$DSH_VALIDATION_ROOT/home"
dsh --profile candidate-0.2 --from-default-profile web --dump-config >/dev/null
```

Use the candidate dependency configuration before adding CPA. Run each group below from the corresponding
pinned repository root. Source tests and packaged artifacts do not prove Desktop native permission
or model behavior. Keep secrets out of output; Host launch logs can contain access tokens.

Browser (the packaged release assets are also available without rebuilding):

```bash
pnpm install --frozen-lockfile
pnpm run check:runtime
pnpm run typecheck
pnpm test
pnpm run build
DSH_TEST_CLI="$DSH_VALIDATION_ROOT/runtime/node_modules/@deepseek-ai/dsh/lib/bin.js" pnpm run test:smoke
(cd packages/browser/bridge-browser && pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts")
```

Load `extensions/dsh-browser/dist/` in Chrome and verify manifest `0.1.16`. The smoke test uses
the complete official npm CLI because the development dependency may lack required runtime peers.
Do not replace smoke assertions or run the installer against an existing formal Web profile.
For release installation, download the checksum-verified bridge tarball to a stable local path
and install it with `dsh plugin --profile <candidate-profile> add <tarball-path>`;
extract and load the matching Chrome zip separately, then reload the extension.

Record/Replay:

```bash
pnpm install --frozen-lockfile
pnpm run validate
pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

Spend:

```bash
pnpm install --frozen-lockfile
pnpm test
pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

Computer Use source requires its pinned pnpm `11.21.0`, not `11.7.0`:

```bash
npm install --prefix "$DSH_VALIDATION_ROOT/computer-tools" --save-exact pnpm@11.21.0
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm install --frozen-lockfile
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm run check
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

Check its `packageManager` integrity and return to the profile's pnpm before installing artifacts.
On Linux, skip the macOS package or disable `computer-engine`, `computer-tools` and
`computer-policy` for a loading-only check. Do not report native behavior as passed there.
Record/Replay retains the packaging fix after `v0.3.0`, whose empty allowBuilds placeholder
breaks pnpm 11 installation. Build the pinned recorder helper on macOS before enabling recording.

### ImageGen source build

The source repository uses a sibling CPA checkout during build only. Pin both
checkouts before installing dependencies; the example below uses CPA `v0.4.11`
(commit `305896e8da2b5fa450728e5a24e8e877e5712f46`) and ImageGen `v0.5.14`
(commit `bde3a303d09278b1c3474129bfe17db0cdaf791d`):

```text
staging/
  dsh-cpa-plugin/
  dsh-image-gen/
```

```bash
git clone --branch v0.4.11 --depth 1 \
  https://github.com/LiuRJ99/dsh-cpa-plugin.git staging/dsh-cpa-plugin
git clone --branch v0.5.14 --depth 1 \
  https://github.com/LiuRJ99/dsh-image-gen.git staging/dsh-image-gen
test "$(git -C staging/dsh-cpa-plugin rev-parse HEAD)" = \
  305896e8da2b5fa450728e5a24e8e877e5712f46
test "$(git -C staging/dsh-image-gen rev-parse HEAD)" = \
  bde3a303d09278b1c3474129bfe17db0cdaf791d

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

The resulting tarball is published as the `v0.5.14` Release asset:

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.14/dsh-image-gen-0.5.14.tgz
```

Download it to a stable local path before installing, so the temporary signed
redirect URL is never written into a long-lived profile lockfile:

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.14/dsh-image-gen-0.5.14.tgz \
  -o /stable/path/dsh-image-gen-0.5.14.tgz
shasum -a 256 /stable/path/dsh-image-gen-0.5.14.tgz
# Expect 7eb68b4805cfb90cb0562bc39972f74a5153010ac1b5a9e0a1a579ea34040949
dsh plugin --profile <candidate-profile> add /stable/path/dsh-image-gen-0.5.14.tgz
```

## macOS services outside the plugin directory

Some plugins install a native component that does **not** live in `node_modules`.
Such a component is not rebuilt by a plugin version bump, so rebuild it
explicitly after changing the plugin version.

- **Computer Use** installs a helper app under `$DSH_HOME/computer-use/`. After
  changing the plugin version, rebuild it with the package's own setup CLI:
  ```bash
  node lib/setup.js --skip-permission-prompt   # build + install only
  ```
  A plugin's `engines.node` floor can also move when a dependency is added, so
  re-check it on upgrade even when the plugin's own version change looks small.
  macOS keys each TCC grant on the helper's bundle id, code signature and on-disk
  path. With the default ad-hoc signature every rebuild changes the code hash and
  macOS asks for Accessibility and Screen Recording again; set
  `DSH_COMPUTER_SIGN_IDENTITY` to a stable code-signing identity to keep grants
  across rebuilds.
- **Browser** builds its extension into the DSH-managed extension directory
  (`$DSH_HOME/browser-extension` by default).

## Verification checklist

**Source provenance:** the commit identities and ImageGen asset digest describe
reviewed release material, not any machine's current installation. Re-resolve
them whenever a source or release changes.

A plugin install or update is complete only when all of these hold:

- the package source is the expected exact version, release/tag target, commit or
  checksum-verified release tarball;
- every runtime target **declared by that package's metadata** (`main`, `exports`,
  `bin`, `dsh.bundle.patch`, or an external CLI path) exists and resolves;
- a newly added runtime dependency resolves from inside the installed package;
- required provider services are installed before their consumers;
- DSH loads the profile with no pending plugin entry;
- no unexpected machine-local path remains outside the ones you intended;
- for the special products above, the browser extension or native daemon setup
  is complete;
- after promotion, the formal DSH process is restarted.

```bash
dsh --profile <candidate-profile> --dump-config
```

## License

This catalog is MIT — see [LICENSE](LICENSE). Each plugin retains its own
upstream license.
