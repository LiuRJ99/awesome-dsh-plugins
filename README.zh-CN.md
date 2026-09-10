# Awesome DSH Plugins

当前 DSH Web 插件目录，以及本 profile 的安装规范。
本文只描述当前可执行的插件、能力、依赖和安装方式，不保存历史故障复盘或过期安装背景。

[English](README.md)

## 当前基线

- DSH Host：`0.1.2-rc.1`
- 已验证机器 Node.js：`24.19.0`
- 正式 Web profile：`/Users/liurenjie/.dsh/profiles/web`，13 个插件
- Profile pnpm：`11.7.0`（store `v11`）——见下方「pnpm 固定」
- 使用 `dsh plugin --profile <profile> add ...` 安装 profile 插件。
- 修改正式 `web` profile 前，先在 candidate profile 验证。
- 不复制其他机器的 profile、lockfile、`node_modules` 或绝对路径。有三处条目
  因交付方式必然携带机器本地路径，已在「当前例外」中逐条登记。

### pnpm 固定

`dsh plugin` 会调用 `PATH` 上的 `pnpm`，因此实际执行的 pnpm 必须与构建该
profile `node_modules` 的 store 一致。

正式 `web` profile 由 pnpm `11.7.0`（store `~/Library/pnpm/store/v11`）安装。
直接使用 pnpm `10.6.4`（store `v10`）会拒绝操作该 profile：

```text
ERR_PNPM_UNEXPECTED_STORE
```

在执行任何 `dsh plugin` 命令前，把固定版本的 pnpm 放到 `PATH` 最前面：

```bash
export PATH="/Volumes/S790C/work/liurenjie/work/dsh-work/.pnpm-11-shim:$PATH"
pnpm --version   # 必须输出 11.7.0
```

在 pnpm 11 下，profile `package.json` 中的 `pnpm` 字段已不再被读取。构建脚本授权
位于 `pnpm-workspace.yaml` 的 `allowBuilds`，当前允许 `@google/genai`、`node-pty`
和 `protobufjs`。

## 来源规则

三条规则决定插件可以从哪里安装。

### 1. 自己 fork 维护的项目一律从仓库安装

所有以 `LiuRJ99` 名义 fork 维护的项目，都从其仓库上固定的 Git release tag 安装，
不使用 registry 包名。

### 2. 本地开发内容必须先发布再安装

本地 checkout 中的改动必须先提交、打 tag、推送并发布，然后由 profile 安装该已发布
tag。直接手工修改 profile `node_modules` 里的文件不算安装：它没有版本控制来源，
`package.json` 也不再描述它，因此其他机器无法复现，下一次重装会静默还原。

### 3. 上游更新先评估 Host 适配

只有在确认上游版本仍支持当前 DSH Host 之后，才考虑采纳。见下方「上游适配评估」。

### 从 Git 主机安装

两种仓库布局决定 `github:` 安装是否可行——因为 pnpm 不会执行 git 托管依赖的构建脚本，
除非消费者显式允许：

- **把构建产物 `lib/` 提交进仓库**的项目，可直接用
  `github:<owner>/<repo>#<tag>` 安装；
- **把构建产物写进 `.gitignore`** 的项目无法这样安装——包会缺失 `main` 指向的文件。
  它们只能走 release tarball 或仓库自带安装器，两者都作为例外记录在下方。

### 来源辨识：npm 同名包

本目录中有四个插件与公共 registry 上的**无关包重名**。按裸包名安装会静默装成
**上游版本而非 fork**——名字相同、项目不同，本 fork 的增强全部丢失：

| 裸包名 | 实际会装到 | 权威来源 |
| --- | --- | --- |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `LiuRJ99/dsh-taskboard-cloader` |
| `dsh-spend` | `nonewind/dsh-spend` | `LiuRJ99/dsh-spend` |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `LiuRJ99/dsh-image-gen` |
| `dsh-github-mcp` | `ZIye1208/dsh-github-mcp` | `GitRuozhi/dsh-github-mcp` |

这四个必须按固定 Git 来源安装，不能按包名安装。`dsh-browser` 和 `dsh-image-gen`
在自己的 README 中也有同类警告。

## 当前插件目录

| 插件 | 能力 | 当前交付方式 | 前置条件 / 依赖 |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI 模型供应商、账号/配额界面、速度模式、图片生成服务 | Git release `v0.4.1` | DSH peer 服务；用户配置 CPA 地址和凭据 |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | WorkBuddy 本地模型供应商 | 私有 Git release `v0.2.3`（SSH） | GitHub SSH 访问权限；WorkBuddy 本地服务（端口 `8318`） |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 浏览器 bridge 工具与 Chrome/Firefox 扩展 | Browser release `v0.1.5` + 仓库安装器；本地 bridge link 是有意设计 | Node/pnpm、Chrome 或 Firefox；bridge 与扩展是一体产品 |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS 应用状态、Accessibility Tree、截图、鼠标键盘输入、MCP 服务 | Git release `v0.1.3` | macOS、Xcode Command Line Tools、重建 native daemon、Accessibility 与 Screen Recording 授权 |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web 侧栏、资源管理器、编辑器、终端、Git、浏览器界面；`ctx.betterSidebar` 服务 | registry 精确版本 `0.18.0` | Taskboard 和 ImageGen 的可选 UI 服务；`0.19.0` 要求更高版本 Host |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | GitHub 官方 MCP server 桥接（`mcp__github__*`）与 REST 文件读取 | Git commit `fb03257c4c0dcfe4fa97c1c693d4eacd9184127c`（上游未发布 tag） | `$DSH_HOME/.env` 中的 `GITHUB_TOKEN` |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA 图片生成、图片模型目录、图片编辑、Gallery 和工作区保存 | Release `v0.5.0` tarball asset，先落到本机稳定文件再 `dsh plugin add` | 先安装 CPA；该仓库 `.gitignore` 了 `lib/`，从 Git 安装会没有入口 |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 受保护的移动端 DSH 会话访问 | registry 精确版本 `0.3.12` | Web Host 和 mobile patch；局域网已启用，远程通路已装未开 |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` 工具与 `open-record-replay` skill，用于录制并回放桌面操作 | Git release `v0.3.0` | macOS 与 Xcode Command Line Tools；需要 `open-record-replay` checkout，通过 profile patch 指定 |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 清理模型侧工具 schema 中多余的沙箱字段 | Git release tag `sandbox-schema-shim-v0.1.1`，package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token 用量、统计、计费计划识别和费用视图 | Git release `v0.6.4` | DSH session、credentials 和 Web UI peer 服务 |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host 权威任务、任务工具、工作区认领、调度和看板 UI | Git release `v0.6.5` | 可选 Better Sidebar；向 Lazy Gate 提供能力元数据 |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 浏览器、Computer Use、Taskboard、录制器四个工具族的会话级门控 | Git release `v0.1.1` | 有 Taskboard 和 Record/Replay 时消费其能力元数据 |

DSH base 和 Web Host 是宿主层，不作为社区插件列在本目录中。

## 依赖关系

```text
DSH base + DSH Web Host
├─ Better Sidebar ── 可选 UI 服务 ──┬─ Taskboard
│                                  └─ ImageGen
├─ CPA Provider ── 必需运行时服务 ── ImageGen
├─ Taskboard ── 能力元数据契约 ── Lazy Gate
├─ Record/Replay ── 能力元数据契约 ── Lazy Gate
├─ Record/Replay ── 调用 bin/orr.js ── open-record-replay checkout
├─ GitHub MCP ── 读取 GITHUB_TOKEN ── $DSH_HOME/.env
├─ Browser bridge ↔ Chrome/Firefox 扩展
└─ Computer Use JS bundle ↔ macOS native daemon + TCC 权限
```

Lazy Gate 门控四个族，每个族只由用户手输的 skill 调用解锁：`browser`、`computer`、
`taskboard`、`recorder`。其中 `recorder` 门控 `orr_*`——它会原样捕获键入文本，
因此绝不能由模型自行触达。

相互独立的插件：

- WorkBuddy provider
- Spend
- Mobile
- Sandbox schema shim

## 安装模式

### 稳定运行或跨机器安装

先使用 candidate profile。所有来源必须固定：

- registry 精确版本；
- 受保护的 Git release tag，或在仓库不发布 tag 时使用精确 commit；
- 从固定源码 commit 构建并验证过的 release tarball。

不要使用 `latest`、`main`、未固定分支，也不要复制另一台机器的 `link:`。

### 本机开发

只有在当前机器完成源码构建并确认运行时入口存在后，才允许在独立的
`web-dev` profile 中使用 `link:`。link 不是跨机器安装格式，也不会执行目标仓库的 build。

改动值得保留后，按上面规则 2 处理：先发布，再安装已发布的 tag。

### 特殊产品

- **Browser：** 不要把 bridge 当作普通 package 直接 `dsh plugin add`。检出 Browser
  release `v0.1.5`，运行 `scripts/install.sh`（Windows 使用对应 installer）。它会构建
  bridge、注册本地 bridge、构建扩展，并复制到 DSH 管理的扩展目录。
- **ImageGen：** 先构建 CPA，再构建 ImageGen 并生成或下载 `v0.5.0` release tarball。
  先落到本机稳定路径再安装；不要让 pnpm 把 GitHub 临时签名重定向 URL 写入长期 lockfile。
- **Computer Use：** 安装 `v0.1.3` release tag 后，用其 setup CLI 重建 native daemon，
  并授予 Accessibility / Screen Recording 权限。
- **WorkBuddy：** 使用私有 `v0.2.3` SSH release tag，并确保 DSH/pnpm 进程可以通过 SSH 访问 GitHub。
- **Record/Replay：** 安装 `v0.3.0` release tag，并在 profile patch 中指向
  `open-record-replay` checkout。

## 安装流程

以下是当前 Web profile 的安装顺序。将占位符替换为目标机器上准备好的固定物料。

```bash
# 先固定与目标 profile store 匹配的 pnpm。
export PATH="/Volumes/S790C/work/liurenjie/work/dsh-work/.pnpm-11-shim:$PATH"

# 新建空 candidate profile 时，先提供 Web Host。
dsh plugin --profile web-candidate add @deepseek-ai/dsh-web-app@0.1.2-rc.1

# 先安装供应商。
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-cpa-plugin#v0.4.1"
dsh plugin --profile web-candidate add \
  "git+ssh://git@github.com:LiuRJ99/dsh-workbuddy-provider.git#v0.2.3"

# 先把 v0.5.0 release asset 下载到稳定本机路径。
dsh plugin --profile web-candidate add /path/to/dsh-image-gen-0.5.0.tgz

# 其他 release tag 或精确 registry 版本。
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

# 先检查组合 profile，再迁移到正式 web。
dsh --profile web-candidate --dump-config
```

正式 profile 使用同样的已验证物料，将 `--profile web-candidate` 换成 `--profile web`。
每次成功安装后让 DSH 自动 reconcile bundle，不要手工维护 `dsh.profile.bundles`。

## ImageGen 源码构建契约

源码仓库只在构建阶段使用相邻 CPA checkout：

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

生成的 tarball 会作为 `v0.5.0` Release asset 发布：

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.0/dsh-image-gen-0.5.0.tgz
```

它才是跨机器安装物料。先下载到稳定的本机路径，再执行 `dsh plugin add`；GitHub 的 Release 下载会重定向到临时签名 URL，不能把这个 URL 写入长期 profile lockfile：

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.0/dsh-image-gen-0.5.0.tgz \
  -o /stable/path/dsh-image-gen-0.5.0.tgz
dsh plugin --profile web-candidate add /stable/path/dsh-image-gen-0.5.0.tgz
```

不要把源码 checkout 复制进 profile，也不要手工修改生成的 tarball。

## 上游适配评估

每个 fork 都跟踪一个上游项目。采纳上游版本前，先对照当前 DSH Host 检查其要求，再明确决策。

| Fork | 上游 | 上游最新 | 已安装 | 评估 |
| --- | --- | --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | 无 release | `v0.4.1` | 上游领先 1 个提交，其中功能类 `0` 个，无需采纳 |
| `dsh-spend` | `nonewind/dsh-spend` | `v0.6.3` | `v0.6.4` | fork 领先上游，无需采纳 |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | 无 release | `v0.1.3` | fork 领先上游，无需采纳 |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | 无 release | `v0.3.0` | fork 领先上游，无需采纳 |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `v0.6.6` | `v0.6.5` | 候选：2 个功能提交（DoD 清单 id、Windows 标题栏布局）。上游仍声明兼容 `0.1.2-rc.1` 且无 peer 变化，可以 rebase——但必须重新叠加本 fork 的 `dsh` 兼容声明与 Better Sidebar 布局修复，不能直接丢弃 |
| `dsh-browser` | `Lum1104/dsh-browser` | `v0.1.3` | `v0.1.5` | 候选：上游约 45 个功能提交（重连所有权、Windows 可移植构建）。本 fork 已高于上游最新 tag，因此这是合并而非版本升级 |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `v0.5.1` | `v0.5.0` | 候选：约 31 个功能提交（BYOK 多 provider、xAI/GLM 图像模型、媒体类型探测）。上游把 peer 放宽为 `>=4.0.0 <5` / `>=3.18.0 <4`，而本 fork 固定精确 Host 版本，合并时必须重新对齐 peer 契约 |

规则：

- 上游版本更高本身不构成升级理由。
- 要求更高 Host 的版本在 Host 升级前完全不可采纳。
- 任何合并后都要重新验证本 fork 的增强：fork 增加了上游没有的兼容声明和集成修复。

## 验证与更新规则

只有满足以下条件才算安装或更新完成：

```bash
dsh --profile web-candidate --dump-config
```

- package 来源是预期的 release tag/精确 version/release tarball；
- `main`、`exports`、`bin` 和 `dsh.bundle.patch` 在物料中真实存在；
- 新增的运行时依赖能从已安装包内部解析；
- 必需的 provider 先于消费者安装；
- 已安装版本与本文档记载一致；
- 除已登记的例外外，没有多余的机器本地路径；
- Web Host 加载时没有 pending plugin entry；
- Browser extension / native setup 等特殊前置已完成；
- candidate 通过后才修改正式 profile。

正式 profile 修改后手动重启 DSH。不要把它的 `package.json`、lockfile 或
`node_modules` 复制到另一台机器。

## 当前例外

### 机器本地路径

有三处有意携带机器本地路径。三者都是特殊产品交付方式的必然结果，不是意外：

| 条目 | 形式 | 原因 |
| --- | --- | --- |
| `@yuxianglin/dsh-bridge-browser` | `link:` | 安装器必须同时构建 bridge 与扩展，再注册已构建的 bridge |
| `dsh-image-gen` | `file:` | 该仓库 `.gitignore` 了 `lib/`，只有 release tarball 携带可运行的包 |
| `record-replay.repoRoot` | 绝对路径 | 插件需要调用本地 `open-record-replay` checkout 的 `bin/orr.js` |

跨机器安装必须各自用本机物料重建这三项，任何一项都不可复制。

### Browser bridge link

`@yuxianglin/dsh-bridge-browser` 在安装 Browser release `v0.1.5` 后有意保留本地 bridge link，
因为仓库安装器必须把 bridge 与扩展一起构建并注册。

该 link 指向安装器的**托管树** `$DSH_HOME/dsh-browser`——一个由标记文件管理的下载副本，
**不是** git checkout——而不是开发用 checkout。扩展构建在 `~/.dsh/browser-extension`。

安装器下载的是 `main` 分支（`scripts/install.sh` 中 `REMOTE_REF="main"`），
而非文档所述的 release tag。在已验证机器上 `main` 仅比 `v0.1.5` 多一个 `docs:` 提交，
构建产物等价，但安装器并未固定 tag。应把它视为受信输入的例外而非固定来源，
每次浏览器安装后都要重新核对。

### Computer Use daemon

native daemon 位于 `$DSH_HOME/computer-use/dsh-computer-daemon.app`，在 `node_modules` 之外，
因此插件升级不会重建它。更换插件版本后，用包自带 setup CLI 重建：

```bash
node lib/setup.js --skip-permission-prompt   # 仅构建并安装
```

新增依赖可能抬高插件的 `engines.node` 下限；即使插件自身版本变化很小，升级时也要复核。

macOS 的 TCC 授权绑定 helper 的 bundle id、代码签名和磁盘路径。默认 ad-hoc 签名下，
每次重建都会改变代码哈希，macOS 会再次询问 Accessibility 与 Screen Recording 权限。
设置 `DSH_COMPUTER_SIGN_IDENTITY` 为稳定签名身份可让授权跨重建保留。

### 构建脚本授权

pnpm `11.7.0` 从 `pnpm-workspace.yaml`（`allowBuilds`）读取构建脚本授权，
而不再读取 profile `package.json` 的 `pnpm` 字段。当前 profile 允许
`@google/genai`、`node-pty` 和 `protobufjs`，因此不再出现 install hook 告警。
插件仓库的 workspace policy 不会随包传递；只对确认过用途的精确脚本授权。

## 许可证

本清单为 MIT；各插件保留其上游许可证。
