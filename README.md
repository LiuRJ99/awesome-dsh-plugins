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
- [Upstream relation](#upstream-relation)
- [Special products](#special-products)
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
8. **Run `dsh plugin` with the pnpm that built the target profile.** `dsh plugin`
   shells out to whatever `pnpm` resolves on `PATH`, and a profile's
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
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI model provider, Codex Responses GPT routing, account/quota UI (including Kimi Code), speed modes, image-generation service | GitHub Release `v0.4.8` | DSH peer services; CPA endpoint and credentials configured by the user |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | Local Tencent WorkBuddy/CodeBuddy model provider for OpenAI-compatible DSH requests | GitHub Release `v0.2.7` | Node `>=20.18.1`; an authenticated WorkBuddy/CodeBuddy desktop session; the local bridge defaults to `127.0.0.1:8318` |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | Browser bridge tools and Chrome/Firefox extension integration | GitHub Release `v0.1.12-dev.1`: bridge tarball + Chrome extension zip; bridge `0.0.12-dev.1` | Node/pnpm; build both bridge and Chrome extension; Firefox needs a separate build and token setup |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS app state, accessibility tree, screenshots, mouse/keyboard input, MCP server | GitHub Release `v0.1.6-dev.2` | macOS, Xcode Command Line Tools, a rebuilt native daemon, Accessibility and Screen Recording grants |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web sidebar, explorer, editor, terminal, Git and browser surfaces; `ctx.betterSidebar` service | Exact registry version `0.24.1` | Optional UI service for Taskboard and ImageGen; `0.24.1` declares DSH `^0.2.0-rc.1` peers; see the optional consumer peer caveat below |
| [`dsh-decision-engine`](https://github.com/LiuRJ99/dsh-decision-engine) | Model-agnostic low-latency decision layer for DSH: pluggable Decision Engine and Providers, finite-candidate decision protocol, and Browser / Computer / Custom environment adapters | GitHub Release `v0.4.18` | DSH peer services; optional `@receptron/laya` for local inference; requires corresponding host tools/plugins if browser/computer adapters are enabled |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | Official GitHub MCP server bridge (`mcp__github__*`) plus a REST file reader | Exact Git commit `5be9077d46bfed66b76843bbcc7bdc459990b3af` (upstream publishes no tags) | `GITHUB_TOKEN` in the DSH process environment; DSH commonly loads it from `$DSH_HOME/.env` |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA-backed image generation, model catalog, image editing, Gallery and workspace save | GitHub Release `v0.5.8` tarball asset; SHA-256 `c7caf43f195d1ff6861c67ec839a0fc47dfe8ebd61fec09fca559aef46a73b59` | Install CPA first; ImageGen was built against CPA `v0.4.8`, and its `>=0.4.0 <0.5.0` peer range covers the catalog target `v0.4.8`. The repository gitignores `lib/`, so a Git install ships no entry point |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | Access to DSH sessions from a mobile device | Exact registry version `0.6.1` | LAN access is separate from optional remote access; remote is off by default, paired devices are fully trusted, LAN uses a pinned local CA, and remote uses the provider's HTTPS endpoint. `0.6.1` requires Node `^22.19 || >=24`; validate the matching mobile client and pairing flow locally |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` tools and the `open-record-replay` skill for recording a demonstrated desktop workflow | Exact Git commit `277a05b527ccfaf8e555933209e70886bf1e545d`; `0.3.3-dev.1` | macOS and Xcode Command Line Tools; exact fork [`open-record-replay`](https://github.com/LiuRJ99/open-record-replay) tag `v0.1.1`, wired through a profile patch |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | Removes redundant sandbox fields from model-facing tool schemas | Git tag `sandbox-schema-shim-v0.1.1`, package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token usage, statistics, billing-plan detection and spend views | GitHub Release `v0.6.7-dev.2` | DSH session, credentials and Web UI peer services |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host-authoritative tasks, task tools, workspace claims, scheduling and kanban UI | GitHub Release `v0.7.4` | Optional Better Sidebar integration; publishes capability metadata to Lazy Gate |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | Session-scoped gating for browser and computer-use by default, plus configured Taskboard/recorder families | GitHub Release `v0.1.4` | Browser/computer are built-in defaults; Taskboard and Record/Replay require capability config plus the adapted skill metadata; includes the Web connection workaround |

### Compatibility note

This candidate targets official `@deepseek-ai/dsh@0.2.0-rc.2`; `0.2.1-alpha.1` is untested.
Browser, Computer Use and Spend use the fixed GitHub Releases above. Record/Replay `0.3.3-dev.1` still uses its exact commit. Do not install forks by colliding bare npm names.
See the [change table and local validation guide](docs/dsh-0.2.0-rc.2.zh-CN.md) and [local validation prompt](docs/local-validation-prompt.zh-CN.md).

737 plugin tests passed. Linux CLI/Web checks verified 13 active plugins while the three native Computer Use rows were disabled.
This does not certify model calls, live browser operations, mobile pairing, native macOS recording or complete UI interactions.

Each package's peer ranges and `dsh.compatibility` are authoritative. Host peer evaluation uses `includePrerelease: true`:
`>=0.1.7-rc.1 <0.2.0` accepts `0.2.0-rc.2`, whereas `^0.1.7-rc.1` does not. The four adapted repositories pin the exact tested Host.
Node 24 was used for validation; still satisfy every package's `engines`.

CPA `v0.4.8` can pull older Host settings/config-editor dependencies. Apply the guide's precise overrides in the candidate profile to keep the relevant official packages at `0.2.0-rc.2`; never patch plugin node_modules.
Taskboard and ImageGen still declare optional Better Sidebar `^0.21.1` peers, which warn with Sidebar `0.24.1`.
Host activation and resource requests passed, but complete sidebar integration needs local validation. No version exemptions were used.

The Record/Replay candidate retains the packaging fix from `v0.3.1`; avoid `v0.3.0` and its unanswered allowBuilds placeholder.
ImageGen `v0.5.8` was built with CPA `v0.4.8` and requires CPA `>=0.4.0 <0.5.0`. Use the checksum-verified release tarball because Git omits `lib/`.
DSH base and Web Host bundles are host layers, not community plugin entries.

## Pinned install examples

Use a newly created `<candidate-profile>` for the first pass. The target profile
must already provide the official DSH Web Host bundle; it is not a community
plugin in this catalog. Merge the guide's profile overrides before installing CPA.
Enable Computer Use only after its macOS native prerequisites are ready; skip that line elsewhere.
These commands use only public, exact sources:

```bash
# Providers first (exact v0.4.8 and v0.2.7 tag commits).
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-cpa-plugin#bd0d80adaac42046a2b54dcf9dc72ce881be5caf"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-workbuddy-provider#4033d36714714e5da01d022cf0930ecd20d739b2"

# Exact registry versions.
dsh plugin --profile <candidate-profile> add dsh-better-sidebar@0.24.1
dsh plugin --profile <candidate-profile> add dsh-mobile@0.6.1

# Fixed sources; published forks use exact release tags.
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-decision-engine#v0.4.18"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-computer-use#v0.1.6-dev.2"
dsh plugin --profile <candidate-profile> add \
  "github:GitRuozhi/dsh-github-mcp#5be9077d46bfed66b76843bbcc7bdc459990b3af"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-record-replay#277a05b527ccfaf8e555933209e70886bf1e545d"
dsh plugin --profile <candidate-profile> add \
  "github:xiaohj233/dsh-compat-shims#ba4088c1a7b77b1c73fd5d5438f46800720d6bcd&path:/packages/sandbox-schema-shim"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-spend#v0.6.7-dev.2"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-taskboard-cloader#v0.7.4"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-tool-lazy-gate#3e8ebe3edbd7db86549fd3bee3ab7b1256e5d2aa"

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
├─ WorkBuddy Provider ── local WorkBuddy/CodeBuddy model bridge
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

## Upstream relation

Several catalog entries are forks. A fork can carry compatibility declarations
and integration fixes that upstream does not have, so a higher upstream version
is **not by itself** a reason to upgrade — and a merge is not a version bump.
Assess each upstream release against the host you actually run before adopting it.

| Fork | Upstream | Notes |
| --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | Upstream has no GitHub Releases; compare upstream commits before merging |
| `dsh-spend` | `nonewind/dsh-spend` | The fork adds an explicit DSH compatibility range; upstream `main` is `v0.6.3` and does not declare that field |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | Public origin has tags `v0.1.1` and `v0.1.2` but no GitHub Releases; historical fork `v0.1.5` carries the host peer-range and security fixes |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | Upstream stops at `0.2.0`, no longer typechecks against DSH ≥ `0.1.2-rc.1`, and has no gate association. The fork also depends on the exact `v0.1.1` tag of [`LiuRJ99/open-record-replay`](https://github.com/LiuRJ99/open-record-replay) for the recorder CLI |
| `dsh-taskboard` | `cloader/dsh-taskboard` | Fork tag `v0.7.4` includes the previously absorbed upstream `v0.6.7` features, recurring scheduled-session reuse, the fork's Better Sidebar header fixes, image attachments, crash-safe configurable data-directory migration, eager tool registration, Better Sidebar `0.19` compatibility, and first-SSE-handshake reconciliation for agent-created tasks; it preserves the fork's multi-repo, permission and speed-routing enhancements |
| `dsh-browser` | `Lum1104/dsh-browser` | Historical fork tag `v0.1.11` includes the fork installer and Host fixes plus rich-text input, bridge-restart resume, dependency-security fixes, prioritized controls for visible dialogs, and opt-in scoped discovery of non-semantic controls |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | Upstream relaxed its peer ranges while this fork pins exact host versions, so a merge must re-align the peer contract |

Rules:

- A higher upstream version is not by itself a reason to upgrade.
- A release that requires a newer host is not adoptable at all until the host moves.
- After any merge, re-verify the fork's own enhancements.
- A fix the fork carried can later appear upstream. Re-check the delta before
  assuming a fork-only patch still has to be reapplied.

## Special products

These need more than a `dsh plugin add`.

- **Browser** — both bridge and browser extension must be ready. Check out exact commit `15b05576ecdb1188fc90d4829a49e843a39bbcd6`,
  install with a frozen lockfile, build the workspace, pack the bridge and add it to a candidate profile; see the [local guide](docs/dsh-0.2.0-rc.2.zh-CN.md).
  Load Chrome's `extensions/dsh-browser/dist/`. The extension manifest remains `0.1.11`; this adaptation changes Host dependencies and the bridge, so that number does not indicate Host compatibility.
  `scripts/install.sh` modifies the `web` profile by default; use the explicit build/pack path for candidate validation.
  Its remote convenience fallback downloads unpinned `main`. Firefox needs a separate `pnpm --filter dsh-browser-extension run build:firefox` build and token setup and was not tested in this round.
- **ImageGen** — to reproduce its build, build CPA from its exact `v0.4.8` tag first, then ImageGen from
  its exact `v0.5.8` tag, and use the published `v0.5.8` release tarball. Its
  asset SHA-256 is
  `c7caf43f195d1ff6861c67ec839a0fc47dfe8ebd61fec09fca559aef46a73b59`.
  Download it to a stable local path before `dsh plugin add`; GitHub serves
  Release downloads through temporary signed redirect URLs, and those must not end
  up in a long-lived lockfile. Do not copy the source checkout into a profile or
  hand-edit the tarball.
- **Computer Use** — install `0.1.6-dev.2` from the exact release tag above, then rebuild the native
  daemon with the package's setup CLI and grant Accessibility / Screen Recording
  separately.
- **Record/Replay** — install `0.3.3-dev.1` from the exact commit above, then point the profile
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

### ImageGen source build

The source repository uses a sibling CPA checkout during build only. Pin both
checkouts before installing dependencies; the example below uses CPA `v0.4.8`
(commit `bd0d80adaac42046a2b54dcf9dc72ce881be5caf`) and ImageGen `v0.5.8`
(commit `501381cd8a7b01d5ee4af89fe5915b8ca91c6991`):

```text
staging/
  dsh-cpa-plugin/
  dsh-image-gen/
```

```bash
git clone --branch v0.4.8 --depth 1 \
  https://github.com/LiuRJ99/dsh-cpa-plugin.git staging/dsh-cpa-plugin
git clone --branch v0.5.8 --depth 1 \
  https://github.com/LiuRJ99/dsh-image-gen.git staging/dsh-image-gen
test "$(git -C staging/dsh-cpa-plugin rev-parse HEAD)" = \
  bd0d80adaac42046a2b54dcf9dc72ce881be5caf
test "$(git -C staging/dsh-image-gen rev-parse HEAD)" = \
  501381cd8a7b01d5ee4af89fe5915b8ca91c6991

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

The resulting tarball is published as the `v0.5.8` Release asset:

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.8/dsh-image-gen-0.5.8.tgz
```

Download it to a stable local path before installing, so the temporary signed
redirect URL is never written into a long-lived profile lockfile:

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.8/dsh-image-gen-0.5.8.tgz \
  -o /stable/path/dsh-image-gen-0.5.8.tgz
shasum -a 256 /stable/path/dsh-image-gen-0.5.8.tgz
# Expect c7caf43f195d1ff6861c67ec839a0fc47dfe8ebd61fec09fca559aef46a73b59
dsh plugin --profile <candidate-profile> add /stable/path/dsh-image-gen-0.5.8.tgz
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
