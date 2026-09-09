# Awesome DSH Plugins

当前 DSH Web 插件目录，以及本 profile 的安装规范。
本文只描述当前可执行的插件、能力、依赖和安装方式，不保存历史故障复盘或过期安装背景。

[English](README.md)

## 当前基线

- DSH Host：`0.1.2-rc.1`
- 已验证机器 Node.js：`24.19.0`
- 正式 Web profile pnpm：`10.6.4`
- 使用 `dsh plugin --profile <profile> add ...` 安装 profile 插件。
- 修改正式 `web` profile 前，先在 candidate profile 验证。
- 不复制其他机器的 profile、lockfile、`node_modules` 或绝对路径。
- 当前正式 profile 只有一个有意保留的本地 link 例外：
  `@yuxianglin/dsh-bridge-browser`，因为它的官方安装器必须同时构建并注册 bridge 与浏览器扩展。

## 当前插件目录

| 插件 | 能力 | 当前交付方式 | 前置条件 / 依赖 |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | CLIProxyAPI 模型供应商、账号/配额界面、速度模式、图片生成服务 | Git release `v0.4.1` | DSH peer 服务；用户配置 CPA 地址和凭据 |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | WorkBuddy 本地模型供应商 | 私有 Git release `v0.2.1`（SSH） | GitHub SSH 访问权限；WorkBuddy 本地服务 |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 浏览器 bridge 工具与 Chrome/Firefox 扩展 | Browser release `v0.1.5` + 仓库专用安装器；本地 bridge link 是有意设计 | 干净的浏览器 checkout、Node/pnpm、Chrome 或 Firefox；bridge 与扩展是一体产品 |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | macOS 应用状态、Accessibility Tree、截图、鼠标键盘输入、MCP 服务 | Git release `v0.1.2` | macOS、Xcode Command Line Tools、Accessibility 和 Screen Recording 权限 |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | Web 侧栏、资源管理器、编辑器、终端、Git、浏览器界面；`ctx.betterSidebar` 服务 | registry 精确版本 `0.18.0` | Taskboard 和 ImageGen 的可选 UI 服务 |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | CPA 图片生成、图片模型目录、图片编辑、Gallery 和工作区保存 | Release `v0.5.0` tarball asset，先落到本机稳定文件再 `dsh plugin add` | 先安装 CPA；源码构建需要 CPA sibling；不要直接从 Git 安装源码 checkout |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 受保护的移动端 DSH 会话访问 | registry 精确版本 `0.3.12` | Web Host 和 mobile patch；移动访问需额外配置 |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 清理模型侧工具 schema 中多余的沙箱字段 | Git release tag `sandbox-schema-shim-v0.1.1`，package path `/packages/sandbox-schema-shim` | DSH base profile |
| [`dsh-spend`](https://github.com/LiuRJ99/dsh-spend) | Token 用量、统计、计费计划识别和费用视图 | Git release `v0.6.4` | DSH session、credentials 和 Web UI peer 服务 |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | Host 权威任务、任务工具、工作区认领、调度和看板 UI | Git release `v0.6.5` | 可选 Better Sidebar；向 Lazy Gate 提供能力元数据 |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 浏览器和 Computer Use 工具族的会话级能力门控 | Git release `v0.1.0` | 有 Taskboard 时消费其能力元数据 |

DSH base 和 Web Host 是宿主层，不作为社区插件列在本目录中。

## 依赖关系

```text
DSH base + DSH Web Host
├─ Better Sidebar ── 可选 UI 服务 ──┬─ Taskboard
│                                  └─ ImageGen
├─ CPA Provider ── 必需运行时服务 ── ImageGen
├─ Taskboard ── 能力元数据契约 ── Lazy Gate
├─ Browser bridge ↔ Chrome/Firefox 扩展
└─ Computer Use JS bundle ↔ macOS native daemon + TCC 权限
```

相互独立的插件：

- WorkBuddy provider
- Spend
- Mobile
- Sandbox schema shim

## 安装模式

### 稳定运行或跨机器安装

先使用 candidate profile。所有来源必须固定：

- registry 精确版本；
- 受保护的 Git release tag；
- 从固定源码 commit 构建并验证过的 release tarball。

不要使用 `latest`、`main`、未固定分支，也不要复制另一台机器的 `link:`。

### 本机开发

只有在当前机器完成源码构建并确认运行时入口存在后，才允许在独立的
`web-dev` profile 中使用 `link:`。link 不是跨机器安装格式，也不会执行目标仓库的 build。

### 特殊产品

- **Browser：** 不要把 bridge 当作普通 package 直接 `dsh plugin add`。检出 Browser
  release `v0.1.5`，运行 `scripts/install.sh`（Windows 使用对应 installer）。它会构建
  bridge、注册本地 bridge、构建扩展，并复制到 DSH 管理的扩展目录。
- **ImageGen：** 先构建 CPA，再构建 ImageGen 并生成或下载 `v0.5.0` release tarball。
  先落到本机稳定路径再安装；不要让 pnpm 把 GitHub 临时签名重定向 URL 写入长期 lockfile。
- **Computer Use：** 安装 `v0.1.2` release tag 后，单独运行 setup CLI，并授予
  Accessibility / Screen Recording 权限。
- **WorkBuddy：** 使用私有 `v0.2.1` SSH release tag，并确保 DSH/pnpm 进程可以通过 SSH 访问 GitHub。

## 安装流程

以下是当前 Web profile 的安装顺序。将占位符替换为目标机器上准备好的固定物料。

```bash
# 新建空 candidate profile 时，先提供 Web Host。
dsh plugin --profile web-candidate add @deepseek-ai/dsh-web-app@0.1.2-rc.1

# 先安装供应商。
dsh plugin --profile web-candidate add \
  "github:LiuRJ99/dsh-cpa-plugin#v0.4.1"
dsh plugin --profile web-candidate add \
  "git+ssh://git@github.com/LiuRJ99/dsh-workbuddy-provider.git#v0.2.1"

# 先把 v0.5.0 release asset 下载到稳定本机路径。
dsh plugin --profile web-candidate add /path/to/dsh-image-gen-0.5.0.tgz

# 其他 release tag 或精确 registry 版本。
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

## 验证与更新规则

只有满足以下条件才算安装或更新完成：

```bash
dsh --profile web-candidate --dump-config
```

- package 来源是预期的 release tag/精确 version/release tarball；
- `main`、`exports`、`bin` 和 `dsh.bundle.patch` 在物料中真实存在；
- 必需的 provider 先于消费者安装；
- 没有非预期的机器本地 `link:`；
- Web Host 加载时没有 pending plugin entry；
- Browser extension / native setup 等特殊前置已完成；
- candidate 通过后才修改正式 profile。

正式 profile 修改后手动重启 DSH。不要把它的 `package.json`、lockfile 或
`node_modules` 复制到另一台机器。

## 当前例外

- `@yuxianglin/dsh-bridge-browser` 在安装 Browser release `v0.1.5` 后，仍因官方安装器必须构建并注册 bridge 与扩展，
  有意保留本地 bridge link。仓库 clean，扩展已构建到 `~/.dsh/browser-extension`。
- 当前 profile 使用 pnpm `10.6.4` 时，安装可能提示 `@google/genai` 和 `protobufjs`
  的脚本被忽略。插件仓库的 workspace policy 不会自动传给 DSH profile；只对确认过用途的精确脚本授权。

## 许可证

本清单为 MIT；各插件保留其上游许可证。
