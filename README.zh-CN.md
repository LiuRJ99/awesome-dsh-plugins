<p align="center">
  <img src="assets/logo.png" alt="Awesome DSH Plugins" width="160">
</p>

<h1 align="center">Awesome DSH Plugins</h1>

<p align="center">
  面向 <a href="https://github.com/deepseek-ai/deepseek-harness">DeepSeek Harness</a> / DSH 的社区插件目录。
</p>

<p align="center">
  <a href="README.md">English</a>
</p>

本目录列出每个公开插件的用途、准确的公开安装来源、前置条件，以及特殊交付例外。
稳定条目使用固定的 registry 版本、Git release/tag、精确 commit 或 release tarball。

本目录**刻意不记录任何机器状态**。具体已装版本、本机路径和 profile 布局属于运行 DSH 的人，
不属于这份目录——这类内容每次安装都会过期。要看某台机器实际装了什么，去查那台机器：

```bash
dsh --profile <profile> --dump-config
```

私有仓库和机器本地集成不会列入这份公开目录，因为它们无法成为对所有 DSH 用户都可移植的安装物料。

## 目录条目规范

公开条目必须回答五个问题：

1. **它做什么？** 用一句话说明能力，并链接到权威仓库。
2. **它从哪里来？** 给出精确 registry 版本、Git release/tag、commit 或 release asset；
   如果安装器存在例外，必须明确写出。
3. **它需要什么？** 列出会影响安装或运行的 Host、运行时、provider、凭据、权限和 native service 前置条件。
4. **谁依赖它？** 说明 provider 先于消费者的顺序，并把可选集成和必需依赖区分开。
5. **如何验收？** 给出可复现的检查，并披露本地路径、信任边界、远程暴露和其他安全敏感副作用。

目录中不得出现某台机器的绝对路径、复制来的 profile 状态、私有凭据，或“当前已安装某版本”的表述。

## 目录

- [目录条目规范](#目录条目规范)
- [安装前须知](#安装前须知)
- [同名冲突：按来源安装，不要按包名安装](#同名冲突按来源安装不要按包名安装)
- [从 Git 主机安装](#从-git-主机安装)
- [插件目录](#插件目录)
- [固定安装示例](#固定安装示例)
- [依赖关系](#依赖关系)
- [与上游的关系](#与上游的关系)
- [特殊产品](#特殊产品)
- [插件目录之外的 macOS 服务](#插件目录之外的-macos-服务)
- [验证清单](#验证清单)
- [许可证](#许可证)

## 安装前须知

1. **固定每一个可移植来源。** 只能用 registry 精确版本、受保护的 Git release/tag、
   精确 commit，或带 checksum 的 release tarball。禁止 `latest`、`main`、未固定分支，
   以及其他机器的 `link:`。Browser 安装器有明确记录的远程 bootstrap 例外；可复现路径是
   精确 tag 的本地 checkout，远程 fallback 只适合作为便利安装，不能视为固定来源。
2. **先在 candidate profile 验证。** 把插件加到一次性 profile 里，确认 DSH 能加载，
   再去动你真正在用的 profile。
3. **provider 先于消费者安装。** 提供模型或运行时服务的插件，必须先于依赖它的插件存在。
4. **不要手工维护 `dsh.profile.bundles`。** 每次 `dsh plugin add` 成功后让 DSH 自动 reconcile。
5. **不要手工修改 profile `node_modules` 里的文件。** 这种改动没有版本控制来源，
   `package.json` 也不再描述它，其他机器无法复现，下一次重装会静默还原。
   如果某个修复值得保留，它属于插件仓库——提交、打 tag、发布，然后安装那个已发布的 tag。
6. **不要在不同机器之间复制 `package.json`、lockfile 或 `node_modules`。**
   改为从固定的来源重新安装。
7. **迁移到正式 profile 后重启。** candidate 已通过不代表正在运行的正式 DSH 进程已经加载新物料，
   必须重启正式 profile。
8. **用构建该 profile 的 pnpm 运行 `dsh plugin`。** `dsh plugin` 会调用 `PATH` 上的 `pnpm`，
   而 profile 的 `node_modules` 记录了创建它的 store。不同大版本的 pnpm 会拒绝操作：

   ```text
   ERR_PNPM_UNEXPECTED_STORE
   ```

   遇到该错误时，应把构建该 profile 的 pnpm 版本放到 `PATH` 最前面，而不是重装整个 profile。
   另外，在 pnpm ≥ 11 下构建脚本授权位于 `pnpm-workspace.yaml`（`allowBuilds`），
   而 profile `package.json` 的 `pnpm` 字段已被忽略。

### `link:` 仅用于本机开发

只有在机器本地的开发 profile 中，且源码 checkout 已构建、运行时入口已确认之后，
才可以使用 `link:` 依赖。`link:` 不会执行目标包的 build，也不是可移植的安装格式。

## 同名冲突：按来源安装，不要按包名安装

本目录中有若干插件与公共 npm registry 上的**无关项目重名**。按裸包名安装会静默装错项目——
名字相同，代码不同：

| 裸包名 | 实际会装到 | 权威来源 |
| --- | --- | --- |
| `dsh-taskboard` | `cloader/dsh-taskboard` | `LiuRJ99/dsh-taskboard-cloader` |
| `dsh-spend` | `nonewind/dsh-spend` | `LiuRJ99/dsh-spend` |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | `LiuRJ99/dsh-image-gen` |
| `dsh-github-mcp` | `ZIye1208/dsh-github-mcp` | `GitRuozhi/dsh-github-mcp` |

这四个绝不能按裸包名安装。执行任何 `dsh plugin add` 之前，先看下方目录表中的固定来源：
Git 交付条目使用 tag/commit，ImageGen 使用经过校验的 release tarball。

## 从 Git 主机安装

`github:` 安装是否可行取决于仓库布局，因为 pnpm 不会执行 git 托管依赖的构建脚本，
除非消费者显式允许：

- **把构建产物 `lib/` 提交进仓库**的项目，可直接用
  `github:<owner>/<repo>#<tag>` 安装；
- **把构建产物写进 `.gitignore`** 的项目则不行：包会带着指向快照中不存在文件的 `main` 到达。
  这类项目只能走 release tarball 或仓库自带的安装器。

如果某个插件从 Git 主机安装后报入口文件缺失，先检查它的 `lib/`（或对应的 `main` 目标）
是否被 gitignore 了。

## 插件目录

| 插件 | 能力 | 安装来源 | 前置条件 |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI 模型供应商、账号/配额界面、速度模式、图片生成服务 | GitHub Release `v0.4.2` | DSH peer 服务；用户自行配置 CPA 地址和凭据 |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 浏览器 bridge 工具与 Chrome/Firefox 扩展集成 | Browser workspace tag `v0.1.5`，通过仓库安装器；bridge 子包自身版本为 `0.0.6` | Node/pnpm；tag 安装器构建 Chrome；Firefox 需要下文的手动 Firefox 构建和 token 配置 |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS 应用状态、Accessibility Tree、截图、鼠标键盘输入、MCP 服务 | GitHub Release `v0.1.4` | macOS、Xcode Command Line Tools、重建 native daemon、Accessibility 与 Screen Recording 授权 |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web 侧栏、资源管理器、编辑器、终端、Git、浏览器界面；`ctx.betterSidebar` 服务 | registry 精确版本 `0.19.0` | Taskboard 和 ImageGen 的可选 UI 服务；`0.19.0` 声明需要 DSH `≥0.1.5-rc.1`（较旧的 `0.18.x` 面向 `0.1.2-rc.1`） |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | GitHub 官方 MCP server 桥接（`mcp__github__*`）与 REST 文件读取 | 精确 Git commit `fb03257c4c0dcfe4fa97c1c693d4eacd9184127c`（上游未发布 tag） | DSH 进程环境中的 `GITHUB_TOKEN`；DSH 通常从 `$DSH_HOME/.env` 加载 |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA 图片生成、图片模型目录、图片编辑、Gallery 和工作区保存 | GitHub Release `v0.5.1` tarball asset；SHA-256 `209e178d9771679d0f6e94b03349774f294b40c38dc976eedc7554957a110b32` | 先安装 CPA；该仓库 `.gitignore` 了 `lib/`，从 Git 安装会没有入口 |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 从移动设备访问 DSH 会话 | registry 精确版本 `0.3.15` | 局域网与可选远程访问分别控制；远程默认关闭，已配对设备完全受信，局域网使用固定本地 CA，远程使用 provider 的 HTTPS 端点。`0.3.15` 支持 DSH `0.1.5-rc.1`（较旧的 `0.3.12` 面向 `0.1.2-rc.1`） |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` 工具与 `open-record-replay` skill，用于录制并回放桌面操作 | GitHub Release `v0.3.1` | macOS 与 Xcode Command Line Tools；使用精确的 fork 版 [`open-record-replay`](https://github.com/LiuRJ99/open-record-replay) tag `v0.1.1`，通过 profile patch 指定 |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 清理模型侧工具 schema 中多余的沙箱字段 | Git tag `sandbox-schema-shim-v0.1.1`，package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token 用量、统计、计费计划识别和费用视图 | GitHub Release `v0.6.5` | DSH session、credentials 和 Web UI peer 服务 |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host 权威任务、任务工具、工作区认领、调度和看板 UI | GitHub Release `v0.6.7` | Better Sidebar 为可选集成；向 Lazy Gate 发布能力元数据 |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 默认门控 browser 与 computer-use，并可按配置门控 Taskboard/录制器工具族 | Git tag `v0.1.1`（无 GitHub Release） | browser/computer 是内置默认；Taskboard 与 Record/Replay 需要 capability 配置和 adapted skill 元数据 |

### 兼容性说明

Record/Replay `v0.3.1` 没有运行时改动——其提交的 `lib/` 与 `v0.3.0` 逐字节相同。
但它仍是应当安装的版本：`v0.3.0` 的 `pnpm-workspace.yaml` 里带着未填写的
`allowBuilds: esbuild: set this to true or false` 占位符，会让 pnpm ≥ 11 以
`ERR_PNPM_IGNORED_BUILDS` 中止整个安装。

每个 package 的 `package.json` 中的精确 peer 范围才是权威依据，不要只根据插件版本号推断兼容性。
这里列出的公开物料主要面向 DSH `0.1.2-rc.1` 这一线。CPA 和 Computer Use 要求 Node `>=22.19`；
Browser 文档要求 `^22.19 || >=24`，ImageGen 的 manifest 声明该范围；Spend 和 Taskboard 声明 Node `>=22`。
ImageGen 另外要求 CPA `>=0.4.0 <0.5.0`、React 18 和 `sharp ^0.35.4`；在较旧 Host 线上，
Taskboard 和 ImageGen 使用可选 peer Better Sidebar `0.18.x`。Record/Replay 有意把 DSH peer 范围写成通配符，
因此必须针对目标 Host 实测兼容性，不能只根据 manifest 推断。迁移到更新 DSH Host 前必须重新检查所有 peer 范围。

DSH base 和 Web Host bundle 是宿主层，不作为社区插件列在本目录中。

## 固定安装示例

第一轮请使用新建的 `<candidate-profile>`。目标 profile 必须已经提供 DSH 官方 Web Host bundle；
它不是本目录中的社区插件。下面只使用公开且精确的来源：

```bash
# 先安装 provider（v0.4.2 tag 对应的 commit）。
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-cpa-plugin#518f864470212cac41e564a3396c01f07658b3a7"

# 精确 registry 版本。
dsh plugin --profile <candidate-profile> add dsh-better-sidebar@0.19.0
dsh plugin --profile <candidate-profile> add dsh-mobile@0.3.15

# 将 GitHub Release/tag 解析为精确 commit。
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-computer-use#7270fdd7aea46913ceec38eb7934073b9bfada7d"
dsh plugin --profile <candidate-profile> add \
  "github:GitRuozhi/dsh-github-mcp#fb03257c4c0dcfe4fa97c1c693d4eacd9184127c"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-record-replay#02c7f51f00e7ad7b740cff123ecb9a4aaf8fc90a"
dsh plugin --profile <candidate-profile> add \
  "github:xiaohj233/dsh-compat-shims#ba4088c1a7b77b1c73fd5d5438f46800720d6bcd&path:/packages/sandbox-schema-shim"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-spend#f4852a14e0a6889356b7f87ab9f07c769dddd2c3"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-taskboard-cloader#995161c9a95f6a6b01e7f43cb0ce301462f46c2a"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-tool-lazy-gate#b01d02cb300c777ce9841041461cf55d73e34284"

# 迁移前检查组合后的 candidate。
dsh --profile <candidate-profile> --dump-config
```

candidate 通过后，用相同的精确来源重复安装到正式 profile，并重启 DSH。
不要把 candidate 的 `package.json`、lockfile 或 `node_modules` 复制到另一台机器。

## 依赖关系

```text
DSH base + DSH Web Host
├─ Better Sidebar ── 可选 UI 服务 ──┬─ Taskboard
│                                  └─ ImageGen
├─ CPA Provider ── 必需运行时服务 ── ImageGen
├─ Taskboard ── 能力元数据契约 ── Lazy Gate
├─ Record/Replay ── 能力元数据契约 ── Lazy Gate
├─ Record/Replay ── 调用 bin/orr.js ── fork 版 open-record-replay checkout
├─ GitHub MCP ── 读取 GITHUB_TOKEN ── DSH 进程环境（通常为 $DSH_HOME/.env）
├─ Browser bridge ↔ Chrome/Firefox 扩展
└─ Computer Use JS bundle ↔ macOS native daemon + TCC 权限
```

Lazy Gate 默认启用 `browser` 和 `computer` 两个族。启用对应 capability，且适配插件发布了
skill 元数据后，也可以门控 `taskboard` 和 `recorder`。每个门控族都只能由用户手输的 skill 调用解锁。
其中 `recorder` 门控 `orr_*`——它会原样捕获键入文本，**绝不能由模型自行触达**。

相互独立的插件（除宿主外不出现在上图）：

- Spend
- Mobile
- Sandbox schema shim

## 与上游的关系

本目录中有若干条目是 fork。fork 可能带有上游没有的兼容声明和集成修复，
因此上游版本更高**本身不构成升级理由**，合并也不等于版本升级。
采纳某个上游版本前，先对照你实际运行的 Host 做评估。

| Fork | 上游 | 说明 |
| --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | 没有 GitHub Release；合并前应比较上游 commit |
| `dsh-spend` | `nonewind/dsh-spend` | fork 增加了明确的 DSH 兼容范围；上游 `main` 为 `v0.6.3`，没有声明该字段 |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | 公开源有 `v0.1.1`、`v0.1.2` tag，但没有 GitHub Release；fork `v0.1.3` 携带 Host 与安全修复 |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | 上游停在 `0.2.0`，已无法对 DSH `≥0.1.2-rc.1` 通过类型检查，也没有门控关联。本 fork 还依赖 [`LiuRJ99/open-record-replay`](https://github.com/LiuRJ99/open-record-replay) 的精确 `v0.1.1` tag 提供录制 CLI |
| `dsh-taskboard` | `cloader/dsh-taskboard` | 上游 `v0.6.7` 带有 fork tag 尚不具备的两项功能（`0.6.6` 的 DoD／Windows 标题栏修复，`0.6.7` 的内置模板本地化与可选的执行会话归档），且仍声明兼容 `0.1.2-rc.1`，可以采纳。上游也已吸收 Better Sidebar 顶栏避让规则，因此只有本 fork 自己的 `dsh.compatibility.dsh` 范围需要重新叠加——该字段上游依然没有 |
| `dsh-browser` | `Lum1104/dsh-browser` | 上游公开最新 tag 是 `v0.1.3`；fork 的 `v0.1.5` 是一次合并，不是丢弃本 fork 安装器和 Host 修复的理由 |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | 上游放宽了 peer 范围，而本 fork 固定精确 Host 版本，合并时必须重新对齐 peer 契约 |

规则：

- 上游版本更高本身不构成升级理由。
- 要求更高 Host 的版本在 Host 升级前完全不可采纳。
- 任何合并后都要重新验证本 fork 的增强。
- fork 曾经携带的修复可能后来被上游吸收。在假定某个 fork 独有补丁仍需重新叠加之前，先重新核对差异。

## 特殊产品

这些插件的安装不止一条 `dsh plugin add`。

- **Browser** —— 不要把 bridge 当作普通 package 直接安装。为了可复现，检出 Browser workspace 的
  **tag** `v0.1.5`，运行本地 `scripts/install.sh`（Windows 使用对应 installer）。它会构建 bridge、
  注册 bridge、构建 Chrome 扩展，并把扩展复制到 DSH 管理的扩展目录：
  ```bash
  git clone --branch v0.1.5 --depth 1 https://github.com/LiuRJ99/dsh-browser.git
  cd dsh-browser
  test "$(git rev-parse HEAD)" = \
    0f1ee137190d28b95e0a95639101591212059549
  ./scripts/install.sh
  ```
  没有完整 checkout 时，远程 convenience installer 会下载 `main`；这条路径有意不算固定安装。
  安装器路径构建 Chrome。Firefox 需要单独手动构建：运行
  `pnpm --filter dsh-browser-extension run build:firefox`，完成扩展 token 配置后再加载生成的 add-on。
- **ImageGen** —— 先从精确的 `v0.4.2` tag 构建 CPA，再从精确的 `v0.5.1` tag 构建 ImageGen，
  并使用发布的 `v0.5.1` release tarball。asset 的 SHA-256 是
  `209e178d9771679d0f6e94b03349774f294b40c38dc976eedc7554957a110b32`。
  先下载到本机稳定路径再执行 `dsh plugin add`；GitHub 的 Release 下载会重定向到临时签名 URL，
  不能让它进入长期 lockfile。不要把源码 checkout 复制进 profile，也不要手工修改 tarball。
- **Computer Use** —— 安装 GitHub Release `v0.1.4` 后，用包自带 setup CLI 重建 native daemon，
  并单独授予 Accessibility / Screen Recording 权限。
- **Record/Replay** —— 安装 GitHub Release `v0.3.1`，然后把 profile patch 的 `repoRoot` 或
  `cliPath` 指向 [fork 版录制器](https://github.com/LiuRJ99/open-record-replay) 的精确 `v0.1.1` tag。
  随后安装依赖并构建 native 组件：
  ```bash
  git clone --branch v0.1.1 --depth 1 https://github.com/LiuRJ99/open-record-replay.git
  cd open-record-replay
  npm install
  npm run build:native
  ```
  插件随包提供的 patch 会有意留空该路径；可以设置 `ORR_REPO_ROOT` / `ORR_CLI_PATH`，或覆盖 profile patch。
  必须使用这个 fork：本插件以会话工作区作为 CLI 的 cwd，而上游录制器以 `process.cwd()` 定位 Swift 包，
  因此 recorder-backed 的权限/录制调用会报 `chdir error: No such file or directory (2)`，质量命令从外部 cwd
  运行时也会失败。该 fork 同时把 native 录制器的部署目标降到 macOS 13 / Swift 5.9。

### ImageGen 源码构建

源码仓库只在构建阶段使用相邻 CPA checkout。安装依赖前先固定两个 checkout；下面示例使用 CPA `v0.4.2`
（commit `518f864470212cac41e564a3396c01f07658b3a7`）和 ImageGen `v0.5.1`
（commit `13fc447c912186c172f856c8610d554842efabff`）：

```text
staging/
  dsh-cpa-plugin/
  dsh-image-gen/
```

```bash
git clone --branch v0.4.2 --depth 1 \
  https://github.com/LiuRJ99/dsh-cpa-plugin.git staging/dsh-cpa-plugin
git clone --branch v0.5.1 --depth 1 \
  https://github.com/LiuRJ99/dsh-image-gen.git staging/dsh-image-gen
test "$(git -C staging/dsh-cpa-plugin rev-parse HEAD)" = \
  518f864470212cac41e564a3396c01f07658b3a7
test "$(git -C staging/dsh-image-gen rev-parse HEAD)" = \
  13fc447c912186c172f856c8610d554842efabff

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

生成的 tarball 会作为 `v0.5.1` Release asset 发布：

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.1/dsh-image-gen-0.5.1.tgz
```

先下载到本机稳定路径再安装，这样临时签名重定向 URL 不会被写入长期 profile lockfile：

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.1/dsh-image-gen-0.5.1.tgz \
  -o /stable/path/dsh-image-gen-0.5.1.tgz
shasum -a 256 /stable/path/dsh-image-gen-0.5.1.tgz
# 应为 209e178d9771679d0f6e94b03349774f294b40c38dc976eedc7554957a110b32
dsh plugin --profile <candidate-profile> add /stable/path/dsh-image-gen-0.5.1.tgz
```

## 插件目录之外的 macOS 服务

部分插件会安装一个**不在 `node_modules` 里**的原生组件。这类组件不会随插件版本升级而重建，
升级插件版本后必须手工重建。

- **Computer Use** 会在 `$DSH_HOME/computer-use/` 下安装一个 helper app。
  更换插件版本后，用包自带 setup CLI 重建：
  ```bash
  node lib/setup.js --skip-permission-prompt   # 仅构建并安装
  ```
  新增依赖也可能抬高插件的 `engines.node` 下限，因此即使插件自身版本变化很小，
  升级时也要复核。macOS 的 TCC 授权绑定 helper 的 bundle id、代码签名和磁盘路径。
  默认 ad-hoc 签名下每次重建都会改变代码哈希，macOS 会再次询问 Accessibility 与
  Screen Recording 权限；把 `DSH_COMPUTER_SIGN_IDENTITY` 设为稳定签名身份可让授权跨重建保留。
- **Browser** 会把扩展构建到 DSH 管理的扩展目录（默认 `$DSH_HOME/browser-extension`）。

## 验证清单

**来源溯源：** 本目录中的 commit 身份和 ImageGen asset digest 已于 `2026-09-11` 核对。
它们描述的是经过审查的物料，不代表任何机器当前已安装的状态；来源或 release 变化后必须重新解析。

只有满足以下条件才算安装或更新完成：

- package 来源是预期的精确 version、release/tag 目标、commit 或经过 checksum 校验的 release tarball；
- 只验证该 package metadata **实际声明的运行时目标**（`main`、`exports`、`bin`、
  `dsh.bundle.patch` 或外部 CLI path），并确认目标存在且可解析；
- 新增的运行时依赖能从已安装包内部解析；
- 必需的 provider 先于其消费者安装；
- DSH 加载该 profile 时没有 pending plugin entry；
- 除你有意保留的例外外，没有多余的机器本地路径；
- 对上述特殊产品，Browser 扩展或 native daemon 的前置已完成；
- 迁移到正式 profile 后，正式 DSH 进程已经重启。

```bash
dsh --profile <candidate-profile> --dump-config
```

## 许可证

本目录为 MIT —— 见 [LICENSE](LICENSE)。各插件保留其上游许可证。
