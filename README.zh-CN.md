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
- [Desktop 部署](#desktop-部署)
- [候选依赖 overrides](#候选依赖-overrides)
- [发布与上游检查](#发布与上游检查)
- [与上游的关系](#与上游的关系)
- [特殊产品](#特殊产品)
- [适配插件源码构建](#适配插件源码构建)
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
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI 模型供应商、GPT/Codex Responses 路由、账号/配额界面（含 Kimi Code）、速度模式、图片生成服务 | GitHub Release `v0.4.8` | DSH peer 服务；用户自行配置 CPA 地址和凭据 |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | 将本地 Tencent WorkBuddy/CodeBuddy 模型接入 DSH 的 OpenAI 兼容 provider | GitHub Release `v0.2.7` | Node `≥20.18.1`；已登录的 WorkBuddy/CodeBuddy 桌面会话；本地 bridge 默认监听 `127.0.0.1:8318` |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 浏览器 bridge 工具与 Chrome/Firefox 扩展集成 | GitHub Release `v0.1.13-dev.1` bridge tarball + Chrome extension zip；bridge `0.0.13-dev.1` | 匹配的 bridge 与 Chrome 扩展物料；仅源码安装时需要同时构建；Firefox 需要单独构建与 token 配置 |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS 应用状态、Accessibility Tree、截图、鼠标键盘输入、MCP 服务 | GitHub Release `v0.1.6-dev.2` | macOS、Xcode Command Line Tools、重建 native daemon、Accessibility 与 Screen Recording 授权 |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web 侧栏、资源管理器、编辑器、终端、Git、浏览器界面；`ctx.betterSidebar` 服务 | registry 精确版本 `0.24.1` | Taskboard 和 ImageGen 的可选 UI 服务；`0.24.1` 声明 DSH `^0.2.0-rc.1` peer；消费者的可选 peer 已覆盖该版本，见兼容性说明 |
| [`dsh-decision-engine`](https://github.com/LiuRJ99/dsh-decision-engine) | 面向 DSH 的模型中立低延迟决策层：提供可插拔决策引擎与 Provider、有限候选集决策协议，以及 Browser / Computer / Custom 环境适配器 | GitHub Release `v0.4.19` | DSH peer 服务；可选固定 SDK `@receptron/laya@0.1.1` 提供本地推理；若启用 browser/computer 适配器需相应宿主工具/插件支持 |
| [`dsh-github-mcp`](https://github.com/GitRuozhi/dsh-github-mcp) | GitHub 官方 MCP server 桥接（`mcp__github__*`）与 REST 文件读取 | 精确 Git commit `5be9077d46bfed66b76843bbcc7bdc459990b3af`（上游未发布 tag） | DSH 进程环境中的 `GITHUB_TOKEN`；DSH 通常从 `$DSH_HOME/.env` 加载 |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA 图片生成、图片模型目录、图片编辑、Gallery 和工作区保存 | GitHub Release `v0.5.10` tarball asset；SHA-256 `ac7876f5a2b72e5ecec40bf365bb6fc1ca1da0c94162a90d872eb3fb914b2b24` | 先安装 CPA；ImageGen 构建时使用 CPA `v0.4.8`，其 `>=0.4.0 <0.5.0` peer 范围覆盖本目录目标 `v0.4.8`。该仓库 `.gitignore` 了 `lib/`，从 Git 安装会没有入口 |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 从移动设备访问 DSH 会话 | registry 精确版本 `0.6.1` | 局域网与可选远程访问分别控制；远程默认关闭，已配对设备完全受信，局域网使用固定本地 CA，远程使用 provider 的 HTTPS 端点。`0.6.1` 要求 Node `^22.19 || >=24`；配套移动客户端及配对流程需本地验证 |
| [`dsh-record-replay`](https://github.com/LiuRJ99/dsh-record-replay) | `orr_*` 工具与 `open-record-replay` skill，用于录制并回放桌面操作 | 精确 Git commit `277a05b527ccfaf8e555933209e70886bf1e545d`；`0.3.3-dev.1` | macOS 与 Xcode Command Line Tools；使用精确的 fork 版 [`open-record-replay`](https://github.com/LiuRJ99/open-record-replay) tag `v0.1.1`，通过 profile patch 指定 |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 清理模型侧工具 schema 中多余的沙箱字段 | Git tag `sandbox-schema-shim-v0.1.1`，package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token 用量、统计、计费计划识别和费用视图 | GitHub Release `v0.6.7-dev.2` | DSH session、credentials 和 Web UI peer 服务 |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host 权威任务、任务工具、工作区认领、调度和看板 UI | GitHub Release `v0.7.7` | Better Sidebar 为可选集成；向 Lazy Gate 发布能力元数据 |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 默认门控 browser 与 computer-use，并可按配置门控 Taskboard/录制器工具族 | GitHub Release `v0.1.7` | browser/computer 是内置默认；Taskboard 与 Record/Replay 需要 capability 配置和 adapted skill 元数据；包含 Web connection workaround |

### 兼容性说明

本轮候选目标为官方 `@deepseek-ai/dsh@0.2.0-rc.2`，未验证 `0.2.1-alpha.1`。
Browser、Computer Use 和 Spend 通过上表固定 GitHub Release 交付；Record/Replay 的 `0.3.3-dev.1` 继续使用精确 Git commit。fork 不按同名裸 npm 包安装。
安装、发布来源、源码构建和 Desktop 验证统一保存在本 README 及英文版中。

737 项插件测试通过；Linux CLI/Web 基础检查中，13 个插件激活，Computer Use 的三个原生条目禁用。
这项 Linux 历史证据不包含模型调用、真实浏览器操作、移动配对、macOS 原生录制或完整 UI 交互。
同一 Host 目标的后续 [Desktop 验收范围](#desktop-验收)另列，不能混为一次全平台验证。

每个 package 的 peer 范围和 `dsh.compatibility` 才是兼容声明依据。Host 的 peer 检查使用 `includePrerelease: true`，
所以 `>=0.1.7-rc.1 <0.2.0` 可以接受 `0.2.0-rc.2`，而 `^0.1.7-rc.1` 不能；不能仅凭范围的外观决定是否改写。
四个适配仓库使用精确的 `0.2.0-rc.2` 声明。Node 24 为本轮验证环境；各包的 `engines` 仍需满足。

CPA `v0.4.8` 的旧版直接依赖可能把 Host settings/config-editor 降到 0.1.7。候选 profile 必须使用
[候选依赖 overrides](#候选依赖-overrides)统一相关官方包；不要修改插件 node_modules。
本轮 fork 版本选择性吸收上游修复：Browser 支持 `browser_open_tab({ active: false })`，保留会话绑定与审批；Taskboard 持久化到期窗口，串行 FIFO 派发，默认间隔一秒；ImageGen 对图库保存失败给出提示，重试只保存图片，不重新生成。保留 Sidebar 0.24.1 集成。自动派生后继卡片、批量永久删除、studio/OAuth 与批量 ZIP 暂缓。

这些目标还保留此前的集成修复：Taskboard 的 producer-owned 调度消息、ImageGen settings entry 标识、
Spend 悬浮组件不遮挡 Taskboard 弹窗、Decision Engine 在 Web Server 就绪后注册路由、
Computer Use 等待 AppKit 启动与首个可访问窗口。Lazy Gate 在 prompt 组装时同步 Host 已收集的工具目录，
包括同轮用户解锁；该同步不会授予权限。

Taskboard `v0.7.7` 和 ImageGen `v0.5.10` 的可选 Better Sidebar peer 已明确覆盖 `^0.21.1 || 0.24.1`。
Host 激活、资源请求和 Taskboard/Gallery 侧栏入口已验证；模型功能与权限仍须按部署环境验收，不使用版本豁免。

Record/Replay 新候选版本保留 `v0.3.1` 的打包修复。不要安装 `v0.3.0`，其未填写的 allowBuilds 占位符会让 pnpm ≥ 11 中止安装。
ImageGen `v0.5.10` 源码构建使用 CPA `v0.4.8`，CPA peer 为 `>=0.4.0 <0.5.0`；必须使用经过 checksum 验证的 Release tarball，因为 Git 忽略了 `lib/`。
DSH base 和 Web Host bundle 是宿主层，不作为社区插件列在本目录中。

### 候选依赖 overrides

针对已验证的 Host `0.2.0-rc.2`，安装 CPA 前将以下设置合并到候选 profile 的
`$DSH_HOME/profiles/<candidate-profile>/pnpm-workspace.yaml`。
保留已有字段与 overrides；其他 Host 必须重新验证，不能直接沿用这些 pin。

```yaml
nodeLinker: hoisted
autoInstallPeers: false
allowBuilds:
  '@google/genai': true
  protobufjs: true
overrides:
  '@deepseek-ai/dsh-atomic-write': 0.2.0-rc.2
  '@deepseek-ai/dsh-brand': 0.2.0-rc.2
  '@deepseek-ai/dsh-config-editor': 0.2.0-rc.2
  '@deepseek-ai/dsh-credentials': 0.2.0-rc.2
  '@deepseek-ai/dsh-llm': 0.2.0-rc.2
  '@deepseek-ai/dsh-settings': 0.2.0-rc.2
  '@deepseek-ai/dsh-timeout': 0.2.0-rc.2
  '@deepseek-ai/dsh-typert-protocol': 0.2.0-rc.2
  '@deepseek-ai/dsh-util-crypto': 0.2.0-rc.2
  '@deepseek-ai/dsh-util-values': 0.2.0-rc.2
```

pnpm 11 的构建授权在该 workspace YAML 中。其他包请求构建脚本时，先检查脚本再按需授权；
不要全局放开构建或使用版本豁免。Host 版本线只比较 `@deepseek-ai/dsh-*`，包含 prerelease；
Cordis 与 Schemastery 使用独立版本线，不混入比较。

## 固定安装示例

第一轮请使用新建的 `<candidate-profile>`。目标 profile 必须已经提供 DSH 官方 Web Host bundle；
它不是本目录中的社区插件。安装 CPA 前先合并上文的候选依赖 overrides。Computer Use 仅在 macOS 原生服务准备好后启用；其他平台跳过该行。下面只使用公开且精确的来源：

```bash
# 先安装 provider（v0.4.8 与 v0.2.7 tag 对应的 commit）。
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-cpa-plugin#bd0d80adaac42046a2b54dcf9dc72ce881be5caf"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-workbuddy-provider#4033d36714714e5da01d022cf0930ecd20d739b2"

# 精确 registry 版本。
dsh plugin --profile <candidate-profile> add dsh-better-sidebar@0.24.1
# 可选：只有需要移动访问时安装；Desktop-only 部署跳过。
dsh plugin --profile <candidate-profile> add dsh-mobile@0.6.1

# 固定来源；已发布 fork 使用精确 release tag。
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-decision-engine#v0.4.19"
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
  "github:LiuRJ99/dsh-taskboard-cloader#v0.7.7"
dsh plugin --profile <candidate-profile> add \
  "github:LiuRJ99/dsh-tool-lazy-gate#v0.1.7"

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
├─ WorkBuddy Provider ── 本地 WorkBuddy/CodeBuddy 模型 bridge
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

- Decision Engine
- Spend
- Mobile
- Sandbox schema shim

## Desktop 部署

官方 Desktop 使用自身 Host 和 Plugin Manager，CLI/Web profile 的安装结果不会自动出现在 Desktop。
安装前核对 Desktop 的实际 Host、Home 与 profile。在独立 candidate Home 中验证，保留已有配置和数据，
通过后再部署并重启正式 Desktop。从 Finder 启动时，使用该版本支持的 Home 配置方式，不假定继承终端的环境变量。
使用 Desktop 自身 Plugin Manager 和目录固定来源，provider 先于消费者安装，合并 CPA overrides。
仅保留官方桌面入口即可；Browser 不需要额外启动器，Computer Use 的原生 helper 仍按实际 provider 保留。

Browser 由 Desktop bridge 和独立加载的 Chrome 扩展组成。在 `chrome://extensions` 核对两个版本，
更新后重载，扩展与 bridge 使用同一固定 Release。桥地址留空时自动探测 `3080 / 3081 / 3090 / 14389 / 43189`；
实际 Host 使用其他端口时，在扩展内配置：

```text
ws://127.0.0.1:<实际 Desktop Host 端口>/ext/bridge
```

Chrome 本机 loopback 无需手动填写 token；Firefox 与远程部署仍按文档认证。
连接成功不等于获得控制授权：会话 `/browser` 门禁和扩展审批仍需遵守。
保留 Computer Use 的 native helper 与 TCC 权限；同一个 helper 同时出现在“辅助功能”和“录屏与系统录音”中，
并非两个客户端。Desktop 自身权限不能替代 helper 权限；原生 preflight 必须在实际 Host 内验证。

迁移前备份会话、附件、Taskboard、Gallery 与配置，迁移后核对标题、历史日志解析、任务工作区归属和图片资源。
不要复制 `node_modules`、改签官方应用或修改应用内 Host。独立 Web 与 Desktop Home 不会自动同步数据。
重启 Host 可能结束 Sidebar 终端。卸载独立 Web 时，仍须保留 Desktop 内部 HTTP / Browser bridge。
Mobile 为可选客户端，Desktop-only 部署跳过。

### Desktop 验收

下表是目标组合的验证证据，不是本机实时安装清单，也不保证其他凭据、平台或模型输入同样通过。
分类只针对表内明确验证的范围。

| 分类 | 功能 / 目标 | 证据与限制 |
| --- | --- | --- |
| 完全可用 | CPA `0.4.8`、WorkBuddy `0.2.7` | 真实文本模型回复；生图/编辑复测成功，但某次 provider 编辑返回只有文字 |
| 完全可用 | Browser `v0.1.13-dev.1` | 540 源码测试；用户重载并批准后，后台开页保留前台，后续输入与点击准确作用于新后台页；Firefox 未测 |
| 完全可用 | Taskboard `0.7.7` | 439 测试通过，2 项可选 Git 测试跳过；两个定时任务获真实模型回复，派发间隔一秒 |
| 完全可用 | ImageGen `0.5.10`、Sidebar `0.24.1` | 148 项 ImageGen 测试；Gallery/Taskboard 入口与已保存图片验证通过；IndexedDB 故障重试由源码测试验证，未在正式图库制造故障 |
| 完全可用 | Computer Use `0.1.6-dev.2` | 原生构建与用户批准后的真实输入/点击；未取得原生权限的 candidate 不能算通过 |
| 完全可用 | Record/Replay `0.3.3-dev.1` | 用户手动 `/open-record-replay` 后确认录制/回放可用；代理不得代为解锁或自行发起录制 |
| 完全可用 | Spend `0.6.7-dev.2` | 真实用量与 UI 通过；费用为估算 |
| 完全可用 | Lazy Gate `0.1.7` | 官方 Host 首轮工具目录、同轮用户解锁通过；顶部汇总加载数量，带可访问名称的刷新图标取代逐卡重复文字 |
| 完全可用，仅建议模式 | Decision Engine `0.4.19` | 显式 `executionMode: advice-only` 只提供 `decision_decide`，拦截动作/任务且不注册 `decision_run`；默认仍为 `execute`，执行模式未测 |
| 需调整 | 可选 Laya SDK `0.1.1` | 推理可用，4 个约束质量样例中 1 个失败；`0.1.2` 未做质量验收，不采纳自动执行 |
| 需调整 | GitHub MCP `1.1.0` | REST `github_file_read` 可用；官方 Host 未将 MCP embedded resource 正文呈现给模型 |
| 完全可用 | Sandbox shim `0.1.1` | read-only/workspace-write 不改 schema；只在已有 danger-full-access 授权下去除冗余字段 |
| 部分不兼容 | 旧历史日志格式 | 旧子代理 descriptor 和 `request/header.config.speed` 可能被目标 Host 拒绝；保留原文件，等待受支持的迁移 |
| 跳过 | Mobile `0.6.1` | 可选客户端；Desktop-only 验收不包含配对与远程访问 |

Host 外供依赖和 prerelease peer 提示应与实际依赖解析分别核对。
Taskboard/ImageGen 的旧可选 Sidebar 警告已由 `^0.21.1 || 0.24.1` 解决，没有版本豁免。
没有内容的旧空会话无法推导标题；孤立任务归属需要权威证据，不能任意重绑定。

## 发布与上游检查

GitHub Release、registry 发布和公开精确 commit 是不同交付形式。
Browser、Taskboard、ImageGen、Lazy Gate、Decision Engine、Computer Use 和 Spend 的目标修复均有公开 Release，
已评估的 main 快照与对应目标 tag 一致。Record/Replay `0.3.3-dev.1` 仍只有公开精确 commit，没有对应 Release。
GitHub MCP 也使用精确 commit，来源仓库没有对应 tag/Release。
Computer Use 已有 GitHub Release/tarball，但 npm 发布 workflow 报 `ENEEDAUTH`；目录不依赖该 registry 发布。
CPA 和 WorkBuddy 的 tag 有公开 Release，采用 Git 交付，未提供 tarball asset。

针对目录目标，解析远端 tag 的 commit，核对 Release 非 draft、asset checksum 和真实运行入口。
另查 registry dist-tags 或 Release 元数据发现新版本；`main` 的 package 版本不代表已经发布。

```bash
gh release view <目录指定的-tag> --repo <owner/repo> \
  --json tagName,isDraft,isPrerelease,publishedAt,assets,url
gh api repos/<owner/repo>/git/ref/tags/<目录指定的-tag>
# annotated tag 继续读取 git/tags/<object.sha>，直到解析为 commit。
npm view <registry-package>@<精确版本> version dist.integrity
```

以下身份用于核对目录固定来源，不是本机安装清单：

| 来源 | 目标 | 提交 |
| --- | --- | --- |
| CPA | `v0.4.8` | `bd0d80adaac42046a2b54dcf9dc72ce881be5caf` |
| WorkBuddy | `v0.2.7` | `4033d36714714e5da01d022cf0930ecd20d739b2` |
| Browser | `v0.1.13-dev.1` | `38d015d6f87cbb57c05539565dc67de0de5dd7d0` |
| Computer Use | `v0.1.6-dev.2` | `189ec1ca73c98c4dc3b7413635351369ec54bc9c` |
| Decision Engine | `v0.4.19` | `eca7ec65ce316de9d17b442ae7a26beb8abc79be` |
| GitHub MCP | 精确 commit | `5be9077d46bfed66b76843bbcc7bdc459990b3af` |
| ImageGen | `v0.5.10` | `73a37d2f6842d12c3b27b74c479f6ae3f0981447` |
| Record/Replay | 精确 commit | `277a05b527ccfaf8e555933209e70886bf1e545d` |
| Sandbox shim | `sandbox-schema-shim-v0.1.1` | `ba4088c1a7b77b1c73fd5d5438f46800720d6bcd` |
| Spend | `v0.6.7-dev.2` | `592db2d44adb7f416a399f65c79e5255c680cf90` |
| Taskboard | `v0.7.7` | `9e1ffad0245e72218597d8aafc72da8c91000458` |
| Lazy Gate | `v0.1.7` | `4dacae05b5b3df698121f41285d38982655c0b90` |
| 录制器 helper | `v0.1.1` | `91188499023cbce9f56c11b58f71bc7e8298a33d` |

| Release asset | SHA-256 |
| --- | --- |
| [Browser bridge tarball](https://github.com/LiuRJ99/dsh-browser/releases/download/v0.1.13-dev.1/yuxianglin-dsh-bridge-browser-0.0.13-dev.1.tgz) | `803232e3837202ddc29091782e7070c43536afb7a6846ada1640540d9a053c8d` |
| [Chrome extension zip](https://github.com/LiuRJ99/dsh-browser/releases/download/v0.1.13-dev.1/dsh-browser-extension-0.1.13-dev.1.zip) | `eb21db4aff93a8655d253df8ca80ff7d13170cbf86a21dac6998dbb68cbe7f7d` |
| [Computer Use tarball](https://github.com/LiuRJ99/dsh-computer-use/releases/download/v0.1.6-dev.2/zibokapi-dsh-codex-computer-use-0.1.6-dev.2.tgz) | `2d37b6a9385e5d2724c1d2feb0a96c44fbbb179ed4a64391b2ef8054810c3e5a` |
| [Decision Engine tarball](https://github.com/LiuRJ99/dsh-decision-engine/releases/download/v0.4.19/dsh-decision-engine-0.4.19.tgz) | `996e9ef7acc14eb84c658c4663effd2c2bc30ebf7522a3187ef231dc4a4b6a5f` |
| [ImageGen tarball](https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.10/dsh-image-gen-0.5.10.tgz) | `ac7876f5a2b72e5ecec40bf365bb6fc1ca1da0c94162a90d872eb3fb914b2b24` |
| [Spend tarball](https://github.com/LiuRJ99/dsh-spend/releases/download/v0.6.7-dev.2/dsh-spend-0.6.7-dev.2.tgz) | `738f10fd9229c8d80e1902c0a5571d7d988da48dae514501d5090e492951f581` |
| [Taskboard tarball](https://github.com/LiuRJ99/dsh-taskboard-cloader/releases/download/v0.7.7/dsh-taskboard-0.7.7.tgz) | `596e1b85b48e47cdeeaabae7a54bd0d8d22ba1a4e6c04fb5c465716af4bd8b40` |
| [Lazy Gate tarball](https://github.com/LiuRJ99/dsh-tool-lazy-gate/releases/download/v0.1.7/dsh-tool-lazy-gate-0.1.7.tgz) | `a0c4c83dd6e6839a4e2e4efc19c9e40ca1549ec9b734e5834e4883c612ab8dc4` |

fork 先查询默认分支上 `sync/upstream-main` 的 open PR，审完整正文、commits、files、reviews 和决策评论，
之后才考虑本地上游 fetch/diff/合并。PR 是冻结快照，不随上游前进刷新。
workflow 成功只说明检测运行过，不说明功能已采纳；无 open PR 时先查 workflow 与上次评估边界。

```bash
gh pr list --repo <fork-owner/repo> --base <默认分支> \
  --head sync/upstream-main --state open
gh pr view <编号> --repo <fork-owner/repo> --json body,commits,files,comments,reviews
gh api --paginate repos/<fork-owner/repo>/pulls/<编号>/files
gh run list --repo <fork-owner/repo> --workflow sync-upstream.yml --limit 3
```

每个 fork 的每日同步 workflow 应保留已有 open 快照与 last-evaluated upstream SHA。
源码/配置冲突与生成物、lockfile 分开评估。选择性吸收或拒绝在关闭 PR 时记录 `partial` / `rejected`，
整包吸收记录 `whole`；评估边界之后的新提交进入下一轮，不自动合并，也不使用 `-X ours`。
自维护仓库核对 main 与 release 来源，区分运行时改动和文档/workflow 改动后再决定是否需要发包。

| 同步 PR（已按 `partial` 关闭） | 已吸收并发布 | 暂缓 / 保留 |
| --- | --- | --- |
| [Browser #5](https://github.com/LiuRJ99/dsh-browser/pull/5) | `6d6ce252`：后台开页、审批提示、前台与控制目标分离；`v0.1.13-dev.1` | 保留 DSH 0.2 peers、独立会话绑定和权限限制 |
| [Taskboard #5](https://github.com/LiuRJ99/dsh-taskboard-cloader/pull/5) | `930bb2c6` / `5746284b`：持久化队列、原子交接、串行派发与可取消等待；`v0.7.7` | 不引入自动后继卡片、runAt 重设计、批量永久删除；保留周期审核卡片及 Sidebar 集成 |
| [ImageGen #5](https://github.com/LiuRJ99/dsh-image-gen/pull/5) | `d9a58cd9`：保存结果传播和只重试保存的 UI；`v0.5.10` | 保留 CPA、事务、删除标记、收藏和工作区元数据；暂缓 studio/OAuth/批量 ZIP |

## 与上游的关系

本目录中有若干条目是 fork。fork 可能带有上游没有的兼容声明和集成修复，
因此上游版本更高**本身不构成升级理由**，合并也不等于版本升级。
采纳某个上游版本前，先对照你实际运行的 Host 做评估。

| Fork | 上游 | 说明 |
| --- | --- | --- |
| `dsh-cpa-plugin` | `router-for-me/dsh-cliproxyapi-provider` | 先查询 fork 的同步 PR，再核对 provider 契约和官方依赖解析 |
| `dsh-spend` | `nonewind/dsh-spend` | fork 保留明确的 DSH 兼容范围与 UI 集成修复；上游版本从公开来源现场查询 |
| `dsh-computer-use` | `geohotstan/dsh-computer-use` | fork 保留 Host peer、原生组件和安全修复；升级 JS 插件还需核对 helper 与权限 |
| `dsh-record-replay` | `humblebanana/dsh-record-replay` | fork 保留目标 Host 类型与门禁适配，并依赖 [`LiuRJ99/open-record-replay`](https://github.com/LiuRJ99/open-record-replay) 的精确 `v0.1.1` tag 提供录制 CLI |
| `dsh-taskboard` | `cloader/dsh-taskboard` | Fork tag `v0.7.7` 包含此前已吸收的上游 `v0.6.7` 特性、周期任务的会话复用、本 fork 的 Better Sidebar 顶栏修复、图片附件、可配置且 crash-safe 的数据目录迁移、工具提前注册、Better Sidebar `0.19` 兼容，以及 agent 创建任务时首次 SSE 握手的状态对账；保留多仓库、权限和调度增强 |
| `dsh-browser` | `Lum1104/dsh-browser` | 历史 fork tag `v0.1.11` 保留本 fork 安装器和 Host 修复，并加入富文本输入、桥重启会话恢复、依赖安全修复、可见对话框优先排序、「不读页面上没渲染的内容」的正文提取修复，以及可按任务开启的非语义控件发现 |
| `dsh-image-gen` | `shanliuling/dsh-image-gen` | fork 保留 CPA provider 与 Sidebar 集成；评估上游 UI/provider 变更时重新核对 Host 契约 |
| `open-record-replay`（辅助 CLI） | `humblebanana/open-record-replay` | fork 固定录制 CLI 的外部 cwd 行为、native 构建目标；它不是独立 DSH 插件 |

每个 fork 先查询 `sync/upstream-main` 的 open PR；有 PR 时审完整正文、commits、files 和决策评论，不先做本地 fetch/diff/试合并。
同步 workflow 成功不代表内容已经吸收。检查步骤见[发布与上游检查](#发布与上游检查)。

规则：

- 上游版本更高本身不构成升级理由。
- 要求更高 Host 的版本在 Host 升级前完全不可采纳。
- 任何合并后都要重新验证本 fork 的增强。
- fork 曾经携带的修复可能后来被上游吸收。在假定某个 fork 独有补丁仍需重新叠加之前，先重新核对差异。

## 特殊产品

这些插件的安装不止一条 `dsh plugin add`。

- **Browser** —— 需要 bridge 与浏览器扩展同时就绪。检出精确 commit `38d015d6f87cbb57c05539565dc67de0de5dd7d0`，
  冻结安装、构建 workspace、打包 bridge，再安装到 candidate profile；详见[源码构建](#适配插件源码构建)。
  Chrome 加载 `extensions/dsh-browser/dist/`。本 Release 的扩展 manifest 为 `0.1.12`；需同时核对实际加载的扩展与 bridge `0.0.13-dev.1`。
  `scripts/install.sh` 默认修改 `web` profile，本轮 candidate 验证使用显式打包安装步骤。
  没有完整 checkout 时，远程 convenience installer 会下载 `main`，这条路径不算固定安装。
  Firefox 需要单独运行 `pnpm --filter dsh-browser-extension run build:firefox` 并完成扩展 token 配置；本轮未验证 Firefox。
- **ImageGen** —— 要复现该版本的构建，先从精确的 `v0.4.8` tag 构建 CPA，再从精确的 `v0.5.10` tag 构建 ImageGen，
  并使用发布的 `v0.5.10` release tarball。asset 的 SHA-256 是
  `ac7876f5a2b72e5ecec40bf365bb6fc1ca1da0c94162a90d872eb3fb914b2b24`。
  先下载到本机稳定路径再执行 `dsh plugin add`；GitHub 的 Release 下载会重定向到临时签名 URL，
  不能让它进入长期 lockfile。不要把源码 checkout 复制进 profile，也不要手工修改 tarball。
- **Computer Use** —— 安装上表精确 release tag 的 `0.1.6-dev.2` 后，用包自带 setup CLI 重建 native daemon，
  并单独授予 Accessibility / Screen Recording 权限。
- **Record/Replay** —— 安装上表精确 commit 的 `0.3.3-dev.1`，然后把 profile patch 的 `repoRoot` 或
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

### 适配插件源码构建

先检查已有 checkout 改动；已有 main 使用 `git pull --ff-only`，缺少的仓库再 clone。
复现时在单独 checkout 固定为上表 tag/commit，不 reset、clean 或丢弃改动。读取适用的仓库 `AGENTS.md`。

以下创建独立的 **CLI 源码测试运行时**，不是安装 Desktop。
使用 Node 24，保留已有 Home，固定完整官方 npm Host：

```bash
export DSH_VALIDATION_ROOT="$PWD/.dsh-validation"
mkdir -p "$DSH_VALIDATION_ROOT/runtime" "$DSH_VALIDATION_ROOT/artifacts"
npm install --prefix "$DSH_VALIDATION_ROOT/runtime" --save-exact \
  @deepseek-ai/dsh@0.2.0-rc.2 pnpm@11.7.0
export PATH="$DSH_VALIDATION_ROOT/runtime/node_modules/.bin:$PATH"
export DSH_HOME="$DSH_VALIDATION_ROOT/home"
dsh --profile candidate-0.2 --from-default-profile web --dump-config >/dev/null
```

安装 CPA 前合并候选 overrides。下面各组命令在相应固定源码仓库根目录执行。
源码测试和打包不能证明 Desktop 原生权限或模型行为正确；输出不得含秘密，Host 启动日志可能包含访问 token。

Browser（无需重建时，也可直接使用发布物料）：

```bash
pnpm install --frozen-lockfile
pnpm run check:runtime
pnpm run typecheck
pnpm test
pnpm run build
DSH_TEST_CLI="$DSH_VALIDATION_ROOT/runtime/node_modules/@deepseek-ai/dsh/lib/bin.js" pnpm run test:smoke
(cd packages/browser/bridge-browser && pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts")
```

Chrome 加载 `extensions/dsh-browser/dist/`，核对 manifest `0.1.12`。
smoke 使用完整官方 npm CLI，因为开发依赖副本可能缺少必需 runtime peer。
不要修改 smoke 断言或让 installer 覆盖已有正式 Web profile。
从 Release 安装时，将 checksum 已核对的 bridge tarball 下载到稳定本地路径，
用 `dsh plugin --profile <candidate-profile> add <tarball-path>` 安装；匹配的 Chrome zip 单独解压、加载并重载。

Record/Replay：

```bash
pnpm install --frozen-lockfile
node scripts/link-dsh.mjs --path "$DSH_VALIDATION_ROOT/runtime/node_modules/@deepseek-ai"
pnpm run validate
pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

Spend：

```bash
pnpm install --frozen-lockfile
pnpm test
pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

Computer Use 源码需要仓库固定的 pnpm `11.21.0`，不能使用 `11.7.0` 替代：

```bash
npm install --prefix "$DSH_VALIDATION_ROOT/computer-tools" --save-exact pnpm@11.21.0
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm install --frozen-lockfile
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm run check
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

核对 `packageManager` integrity，操作 profile 前恢复相应 pnpm。
Linux 跳过 macOS 包，或在仅加载检查时禁用 `computer-engine`、`computer-tools`、`computer-policy`，不能报告原生功能通过。
Record/Replay 保留 `v0.3.0` 之后的打包修复；该旧版本的空 allowBuilds 占位符会破坏 pnpm 11 安装。
启用录制前，须在 macOS 构建固定来源的录制器 helper。

### ImageGen 源码构建

源码仓库只在构建阶段使用相邻 CPA checkout。安装依赖前先固定两个 checkout；下面示例使用 CPA `v0.4.8`
（commit `bd0d80adaac42046a2b54dcf9dc72ce881be5caf`）和 ImageGen `v0.5.10`
（commit `73a37d2f6842d12c3b27b74c479f6ae3f0981447`）：

```text
staging/
  dsh-cpa-plugin/
  dsh-image-gen/
```

```bash
git clone --branch v0.4.8 --depth 1 \
  https://github.com/LiuRJ99/dsh-cpa-plugin.git staging/dsh-cpa-plugin
git clone --branch v0.5.10 --depth 1 \
  https://github.com/LiuRJ99/dsh-image-gen.git staging/dsh-image-gen
test "$(git -C staging/dsh-cpa-plugin rev-parse HEAD)" = \
  bd0d80adaac42046a2b54dcf9dc72ce881be5caf
test "$(git -C staging/dsh-image-gen rev-parse HEAD)" = \
  73a37d2f6842d12c3b27b74c479f6ae3f0981447

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

生成的 tarball 会作为 `v0.5.10` Release asset 发布：

```text
https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.10/dsh-image-gen-0.5.10.tgz
```

先下载到本机稳定路径再安装，这样临时签名重定向 URL 不会被写入长期 profile lockfile：

```bash
curl -fL \
  https://github.com/LiuRJ99/dsh-image-gen/releases/download/v0.5.10/dsh-image-gen-0.5.10.tgz \
  -o /stable/path/dsh-image-gen-0.5.10.tgz
shasum -a 256 /stable/path/dsh-image-gen-0.5.10.tgz
# 应为 ac7876f5a2b72e5ecec40bf365bb6fc1ca1da0c94162a90d872eb3fb914b2b24
dsh plugin --profile <candidate-profile> add /stable/path/dsh-image-gen-0.5.10.tgz
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

**来源溯源：** 本目录中的 commit 身份和 ImageGen asset digest 描述的是经过审查的发布物料，
不代表任何机器当前已安装的状态；来源或 release 变化后必须重新解析。

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
