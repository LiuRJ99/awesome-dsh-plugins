# 官方 DSH 0.2.0-rc.2 插件适配与本地验证

本轮目标为官方 npm `@deepseek-ai/dsh@0.2.0-rc.2`，使用 Node 24 在 Linux x64 完成开发和基础验证。
`0.2.1-alpha.1`、其他未来版本和 Desktop 自带的不同 Host 版本没有在此验证。
以下是可复现的来源与验证证据，不包含某台机器的配置、凭据或 profile 副本。

## 插件调整表

| 插件 | 目标版本 | 调整 | 已验证 | 本地继续验证 |
| --- | --- | --- | --- | --- |
| CPA | 0.4.8 | 保留插件；候选 profile 精确 overrides 统一官方依赖 | Host 激活 | CPA 连通、模型和图片服务 |
| WorkBuddy | 0.2.7 | 保留固定 Git 提交，锁文件固定实际提交 | Host 激活 | 登录会话、bridge、模型 |
| Browser | workspace 0.1.12-dev.1 / bridge 0.0.12-dev.1 | DSH peers、开发依赖、锁文件改为 0.2.0-rc.2；CI 使用完整官方 npm Host | 532 测试、类型检查、Chrome 构建、真实 Host 烟雾测试 | 加载扩展、真实页面工具、Firefox（本轮未测） |
| Computer Use | 0.1.6-dev.2 | DSH peers、开发依赖、锁文件；fake-daemon 平台夹具及 Linux 拒绝加载测试；CI 目标同步 | 153 测试、类型检查、JS/声明构建 | macOS native、TCC 权限、真实应用；Linux 禁用三个原生条目 |
| Better Sidebar | 0.24.1 | 从 0.21.1 升级为 DSH 0.2 peer 版本 | Host 激活、客户端资源 | 编辑器、终端、Git、与消费者的 UI 集成 |
| Decision Engine | 0.4.19 | 新增仅建议模式；禁用时仍注册控制技能 | Host 激活、328 源码测试、仅建议决策与执行拦截 | 模型质量、可选 Laya SDK、执行模式下的 browser/computer 适配器 |
| GitHub MCP | 1.1.0 | 更新固定上游提交 | Host 激活 | 凭据、MCP 和文件读取 |
| ImageGen | 0.5.9 | 保留 checksum 已核对的 Release tarball | Host 激活 | 生成/编辑、Gallery、Sidebar 集成 |
| Mobile | 0.6.1 | 从 0.4.6 升级 | Host 激活 | 移动客户端、配对、远程连接 |
| Record/Replay | 0.3.3-dev.1 | 精确 DSH peers/compatibility；开发工具固定 pnpm 11.7.0 | 26 测试、构建、Host 激活 | macOS recorder native、权限、用户手动录制与回放 |
| Sandbox schema shim | 0.1.1 | 保留固定 Git 提交与包子路径 | Host 激活 | 模型工具 schema |
| Spend | 0.6.7-dev.2 | 精确 DSH peers/compatibility、锁文件；固定 pnpm 和 CI 工具 | 26 测试、真实 usageStats/query RPC | 真实模型用量、计费与完整 UI |
| Taskboard | 0.7.6 | 保留固定 Git 提交 | Host 激活、客户端资源 | CRUD、调度、Lazy Gate、Sidebar 集成 |
| Lazy Gate | 0.1.6 | 同步当前请求目录与可信用户解锁；显示插件、资源与会话状态 | 39 源码测试、官方 SystemPrompt 回归、首轮目录与同轮解锁 | 用户手势、会话恢复、实际 browser/computer/recorder 权限 |

Browser `v0.1.12-dev.1`、Computer Use `v0.1.6-dev.2` 与 Spend `v0.6.7-dev.2` 已发布固定 GitHub Release；Record/Replay 仍使用精确 commit。
插件测试共 **737 项通过**（532 + 153 + 26 + 26）。录制器 fork `open-record-replay@v0.1.1` 另有 10 项 Node 测试通过；其完整安装和 Swift 构建需要 macOS。

Web 基础检查验证了认证 HTML、7 个应用/插件资源、13 个 active 插件、全部启用条目的激活状态、Spend 统计 RPC 和 Browser 桥发现接口。
Browser 烟雾测试覆盖发现、token 认证、会话 create/list/history 和 Host 重启后的 projection 恢复，不等同于真人浏览器操作。

## 固定来源

机器可读的全部来源位于 [dsh-0.2.0-rc.2.json](../compatibility/dsh-0.2.0-rc.2.json)。本次开发仓库固定为：

| 仓库 | 提交 |
| --- | --- |
| LiuRJ99/dsh-browser | `15b05576ecdb1188fc90d4829a49e843a39bbcd6` |
| LiuRJ99/dsh-computer-use | `189ec1ca73c98c4dc3b7413635351369ec54bc9c` |
| LiuRJ99/dsh-record-replay | `277a05b527ccfaf8e555933209e70886bf1e545d` |
| LiuRJ99/dsh-spend | `592db2d44adb7f416a399f65c79e5255c680cf90` |

已有 checkout 请先检查未提交改动，再 `git fetch origin`、在本地 `main` 上 `git pull --ff-only`。
远端后续可能继续更新；复现本轮请核对上表提交，必要时另行 clone 并 checkout 精确 commit。不要 reset、clean 或丢弃已有改动。
Browser 的构建产物被忽略，不能直接把 Git 仓库当成 bridge 安装包；另外三个仓库提交了 `lib/`，可按精确 Git 来源安装。

## 独立官方运行时与 profile

在自选验证目录执行，先确保 Node 24 和 npm 可用。创建独立运行时及 `DSH_HOME`，保留原来的 Web/Desktop 配置。

```bash
export DSH_VALIDATION_ROOT="$PWD/.dsh-validation"
mkdir -p "$DSH_VALIDATION_ROOT/runtime" "$DSH_VALIDATION_ROOT/artifacts"
npm install --prefix "$DSH_VALIDATION_ROOT/runtime" --save-exact \
  @deepseek-ai/dsh@0.2.0-rc.2 pnpm@11.7.0
export PATH="$DSH_VALIDATION_ROOT/runtime/node_modules/.bin:$PATH"
export DSH_HOME="$DSH_VALIDATION_ROOT/home"
dsh --profile candidate-0.2 --from-default-profile web --dump-config >/dev/null
```

使用相同的 pnpm 操作该 profile。模型凭据通过本地安全配置提供，不要写进 Git、提示词或聊天。
Desktop 可作为后续测试，但必须先查看实际 Host 版本；Web candidate 的插件不会自动出现在 Desktop profile。

## 候选 profile 依赖解析

CPA 的旧直接依赖会在 hoisted 布局下覆盖 Host 的部分官方服务。安装 CPA 前，在新 profile 的
`$DSH_HOME/profiles/candidate-0.2/pnpm-workspace.yaml` 中**合并**下面设置，保留已有字段；不要覆盖原文件或现有 overrides。
这些精确 overrides 仅针对本轮已验证的 Host。

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

pnpm 11 的 build 授权在 workspace YAML 中。其他包请求构建脚本时，先检查对应脚本，再按实际需要授权，不能全局放开。
`dsh-mobile@0.6.1` 如被最短发布时间限制拦截，可仅将该精确版本加入 `minimumReleaseAgeExclude`，保留已有列表。
WorkBuddy 某些安装形式会丢掉 manifest 的 commit fragment；请核对 pnpm-lock.yaml 的实际 resolution 固定为表中 SHA，之后使用冻结安装。

## 构建四个适配插件

先拉取四个仓库到各自源码目录并核对固定提交。下列步骤都在对应仓库根目录执行；`DSH_VALIDATION_ROOT`、PATH 与 `DSH_HOME` 沿用上一步。

Browser：

```bash
pnpm install --frozen-lockfile
pnpm run check:runtime
pnpm run typecheck
pnpm test
pnpm run build
DSH_TEST_CLI="$DSH_VALIDATION_ROOT/runtime/node_modules/@deepseek-ai/dsh/lib/bin.js" pnpm run test:smoke
(cd packages/browser/bridge-browser && pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts")
```

Chrome 加载该源码目录的 `extensions/dsh-browser/dist/`。扩展 manifest 仍为 `0.1.11`，bridge 为 `0.0.12-dev.1`。
使用完整官方 npm CLI 做 smoke，是因为 pnpm 开发副本在 `autoInstallPeers: false` 时可能缺少官方 runtime 的必需 peer。
不要为了过 smoke 而删断言，也不要用默认会改写 `web` profile 的 Browser installer 覆盖原配置。

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

Computer Use 需要仓库固定的 pnpm `11.21.0`，不能用 `11.7.0` 替代：

```bash
npm install --prefix "$DSH_VALIDATION_ROOT/computer-tools" --save-exact pnpm@11.21.0
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm install --frozen-lockfile
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm run check
PATH="$DSH_VALIDATION_ROOT/computer-tools/node_modules/.bin:$PATH" pnpm pack --pack-destination "$DSH_VALIDATION_ROOT/artifacts"
```

pnpm 的 SHA-512 校验应与该仓库 `packageManager` 一致。操作 profile 时恢复 `11.7.0` 的 PATH。

## 安装与启动

先用 README 的固定来源安装 CPA、WorkBuddy，然后安装 Sidebar/Mobile、其他插件和本地适配包。不要裸装同名冲突的包。
ImageGen 从 README 指定的公开 Release 下载，检查 SHA-256 为
`a16042e2a9d16dada99da9d24a0c3356b117e1d91b52a3a9efb5aa92f8e410d5` 后再从稳定本地路径安装。

```bash
dsh plugin --profile candidate-0.2 add "$DSH_VALIDATION_ROOT/artifacts/dsh-spend-0.6.7-dev.2.tgz"
dsh plugin --profile candidate-0.2 add "$DSH_VALIDATION_ROOT/artifacts/dsh-record-replay-0.3.3-dev.1.tgz"
dsh plugin --profile candidate-0.2 add "$DSH_VALIDATION_ROOT/artifacts/yuxianglin-dsh-bridge-browser-0.0.12-dev.1.tgz"
# 以下原生包仅在 macOS daemon 和权限准备好后启用：
dsh plugin --profile candidate-0.2 add "$DSH_VALIDATION_ROOT/artifacts/zibokapi-dsh-codex-computer-use-0.1.6-dev.2.tgz"
dsh --profile candidate-0.2 --no-open --host 127.0.0.1 --port 3080
```

Linux 可不安装 Computer Use；如为加载检查安装了包，应通过 profile patch 禁用 `computer-engine`、`computer-tools`、`computer-policy`。
修改 bundle 列表应由 DSH 自动 reconcile；不要手工编辑 `dsh.profile.bundles` 或插件 node_modules。启动日志包含访问 token，分享结果时务必脱敏。

macOS 还需安装 Xcode Command Line Tools，为已安装的 Computer Use 运行本地包里的 `lib/setup.js`，构建 daemon 并授予 Accessibility/Screen Recording；不要通过裸 `npx` 换成不确定的 registry 版本。
Record/Replay 使用 `LiuRJ99/open-record-replay` 精确 tag `v0.1.1`（`91188499023cbce9f56c11b58f71bc7e8298a33d`），在 macOS 安装依赖、构建 native，再设置本地 `ORR_REPO_ROOT` 或 `ORR_CLI_PATH`。
录制器只在用户手动调用 `/open-record-replay` 后测试，保留 Lazy Gate 的用户手势约束。

## 未解决的兼容警告与验收

Taskboard `v0.7.6` 和 ImageGen `v0.5.9` 已在侧栏集成测试后，将可选 Better Sidebar peer 声明为 `^0.21.1 || 0.24.1`。Taskboard 全部 431 项源码测试（包括侧栏相关测试）和 ImageGen 的 29 项相关测试通过；Host 激活、资源与 Taskboard/Gallery 侧栏入口已验证。此修复不代表图片编辑 provider、所有历史会话和本机权限均已通过，不加 version exemption。

本地验收请按顺序记录结果：

1. Plugin Manager：版本、Host runtime、peers、不兼容原因；逐个检查 enabled/active 状态与资源请求。
2. 已有源码测试、Browser 真实 Host smoke；保留实际测试数量、失败和跳过项。
3. Browser 真人页面操作、Sidebar 编辑器/终端/Git、Taskboard CRUD/调度与 ImageGen Gallery。
4. 有可用服务/凭据时验证 CPA、WorkBuddy、GitHub MCP、Decision Engine、图片模型和 Spend 的真实用量。
5. Mobile 配对与远程访问；macOS Computer Use；用户手动触发的录制与回放；Lazy Gate 解锁/重锁。

最终输出表格：插件、实际版本、Host 版本、加载状态、功能验证、错误或警告、后续动作。
源码推送不代表 npm 发布，也不代表 GitHub Actions 或上述本地端到端测试已经通过。

## 后续发布修复

Taskboard `v0.7.4` 修复 DSH 0.2 producer-owned 调度消息；ImageGen `v0.5.8` 修复 settings entry id；Spend `v0.6.7-dev.2` 修复悬浮组件遮挡 Taskboard 弹窗；Decision Engine `v0.4.18` 在 Web Server 就绪后注册 provider 路由；Computer Use `v0.1.6-dev.2` 修复 AppKit 启动通知与首个 AX 窗口的等待。

上述源码及 Browser、Record/Replay 的测试共 1636 项通过，另有录制器 10 项 Node 测试和 Computer Use 30 项 Swift 测试通过。Taskboard/ImageGen 后续 release 已修复 Sidebar `0.24.1` 可选 peer 声明，并保留相关 UI 集成测试；不使用版本豁免。Laya SDK/模型、图像编辑的 provider 网络调用，以及用户手动录制仍需按部署环境验证。

## 桌面门禁与决策建议模式补充

Lazy Gate `v0.1.6` 修复官方 `0.2.0-rc.2` 的组装时序差异：Host 先收集工具，再运行 `system-prompt/assemble`；仅更新 scoped restriction 不会同步本轮已收集的目录。插件现在同时过滤该目录，并在同轮恢复用户刚解锁且仍满足其他限制的工具，再交给下游 schema 中间件。`v0.1.5` 的当前请求目录修复不完整，应使用 `v0.1.6`。设置页提供资源刷新及“插件已禁用 / 资源缺失 / 已锁定 / 已解锁 / 下次会话生效”等独立状态，展示状态不会自动授权。

Decision Engine `v0.4.19` 增加 `executionMode: advice-only`：只提供 `decision_decide`，不注册 `decision_run`；工具与 service 层拒绝执行动作和任务。`enabled: false` 时仍保留 `decision-control` 技能声明，便于门禁识别插件处于禁用状态。默认值仍为 `execute`，需要仅建议模式时应在 candidate 设置中显式选择，再验证和部署。建议模式可验证本地 Provider 的决策结果，但不保证模型一定满足约束或选对候选，质量验收仍需保留。

Sandbox schema shim `0.1.1` 在 `workspace-write` 和 `read-only` 下不改变 schema；只在已有 `danger-full-access` 会话去除冗余提权字段，不扩大实际权限。

Desktop 的隔离 candidate 如果使用新的启动器或签名，macOS 可能重新计算原生组件的权限归属。原路径的文件存在或从终端读取权限成功，不能替代 Desktop Host 内的 preflight；未取得授权的原生功能应报告跳过或失败，不能报通过。
