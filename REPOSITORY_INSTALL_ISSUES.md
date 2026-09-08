# 仓库安装问题记录

> 本文件记录将当前插件从本地 `link:` 安装切换为远程 Git 仓库安装时遇到的问题、验证结果与上游安装约定。
>
> 验证基线：DSH `0.1.2-rc.1`、Node `v24.19.0`、Web profile、pnpm `11.x`。

## 结论先行

DSH 上游没有另一套复杂的“仓库插件安装器”。官方入口就是：

```bash
dsh plugin --profile web add <package-spec>
```

`dsh plugin` 会在 profile 目录转发执行 `pnpm`，安装成功后再根据已安装包的
`dsh.bundle.patch` 自动维护 `dsh.profile.bundles`。

因此：

- 不要手工修改 `dsh.profile.bundles`；
- 不要手工把 profile 的 `link:` 改成 Git URL；
- 远程安装可以使用 registry、tarball 或 pinned Git commit；
- 本地开发才使用 `link:`；
- Git 仓库必须先满足普通 pnpm package 的安装契约，否则换成 Git URL 不会自动修复仓库本身的问题。

上游参考：

- [DSH 插件打包与安装文档](https://github.com/deepseek-ai/DeepSeek-Harness/blob/master/docs/user/develop/basic/publish.zh.md)
- [DSH profile README](https://github.com/deepseek-ai/deepseek-harness/blob/master/README.md)

## 推荐命令

### 远程仓库安装

```bash
dsh plugin --profile web add "github:owner/repository#<commit-or-tag>"
```

或：

```bash
dsh plugin --profile web add "git+https://github.com/owner/repository.git#<commit>"
```

monorepo 子目录使用 pnpm 的 Git path 语法：

```bash
dsh plugin --profile web add \
  "github:owner/repository#<commit>&path:/packages/plugin"
```

### 本地开发安装

```bash
dsh plugin --profile web add -w \
  "package-name@link:/absolute/path/to/plugin"
```

`link:` 只代表本地开发挂载，并不应作为生产 profile 的安装契约。

## 已验证的安装结果

| 插件 | 原始 Git 安装结果 | 结论 |
| --- | --- | --- |
| `dsh-taskboard` | 可以安装并加载 Host 入口 | Git package 契约基本正常 |
| `dsh-tool-lazy-gate` | 可以安装并加载 Host 入口 | Git package 契约基本正常 |
| `dsh-spend` | 可以安装并加载 Host 入口 | Git package 契约基本正常 |
| `@LiuRJ99/dsh-workbuddy-provider` | 可以安装并加载 Host 入口，但有 peer 警告 | 安装可行，metadata 仍需整理 |
| `@zibokapi/dsh-codex-computer-use` | package 可以安装 | 启动还依赖 native helper，安装本身不能提供 daemon |
| `@LiuRJ99/dsh-cpa-plugin` | 允许相关依赖构建脚本后可以安装 | 需要 profile 的 build policy 配置 |
| `dsh-image-gen` | 原始远程 commit 的 Git 安装失败 | `prepare`、本地 sibling link、构建产物均有问题 |
| `@yuxianglin/dsh-bridge-browser` | monorepo 子目录 package 可解析，但启动时缺少 `lib/index.js` | Git checkout 未包含运行时构建产物 |

## 具体问题

### 1. Git package 的生命周期脚本受 `allowBuilds` 控制

Git 依赖可能执行 `prepare`、`prepack` 或其他 install lifecycle。当前 DSH profile
使用 pnpm 的 build-script 安全策略，未允许的脚本会被阻止。

已经实际遇到：

- CPA 安装时，`@google/genai` 和 `protobufjs` 的构建脚本被阻止；
- Web Host 在干净 profile 中安装时，`koffi` 的构建脚本被阻止；
- Git package 自己的 `prepare` 也可能被阻止。

这是 pnpm 的安装策略，不是 DSH module loader 的问题。需要时应只允许确实需要的包：

```yaml
allowBuilds:
  koffi: true
  '@google/genai': true
  protobufjs: true
```

不能把“允许所有 build script”作为仓库安装的默认方案。

### 2. image-gen 不能直接作为当前 Git package 安装

原始远程版本的主要问题：

1. package 有安装期 build/prepare；
2. devDependency 使用了本地 sibling：

   ```json
   "@LiuRJ99/dsh-cpa-plugin": "link:../dsh-cpa-plugin"
   ```

   Git checkout 是隔离目录，不存在这个相对路径；
3. `main`/`exports` 指向 `lib`，但 `lib` 被 `.gitignore`，Git checkout 中没有运行时入口。

因此仅把 profile 中的 `link:` 替换成 `github:`，会把原来隐藏的仓库发布问题暴露出来。

### 3. browser bridge 是 monorepo 子包

`dsh-browser` 同时包含 bridge、Chrome extension 和 Firefox extension，不能把仓库根目录
当作普通单 package 安装。

测试使用的子目录形式是：

```text
github:LiuRJ99/dsh-browser#<commit>&path:/packages/browser/bridge-browser
```

但原始远程版本的 bridge package 虽然能被 pnpm 解析，启动时会因为 `lib/index.js` 不在
Git checkout 中而失败。当前仓库的 managed installer 采用的是：

```text
clone → install → build bridge → link 到 profile
```

这属于项目自己的 source installer，不等同于普通 Git package 安装。

### 4. computer-use 还有独立的 native 前置条件

Git package 安装成功不等于 Computer Use 可启动。Host 启动还需要：

```text
computer-use/dsh-computer-daemon.app/...
```

如果 native helper 没有安装，DSH boot 会失败。这个问题不能通过修改 npm dependency 或
profile bundle 解决。

### 5. peer 警告不应直接等同于运行时失败

在干净 profile 中执行：

```bash
pnpm peers check
```

会看到大量 DSH Host peer 缺失或冲突。这是因为 DSH 的 Host bundles 由 DSH 安装本身和
profile fallback 提供，并不一定作为普通 pnpm dependencies 出现在 profile manifest 中。

在临时 profile 中补齐临时构建产物、允许必要的 build script，并提供 native helper 后，完整 Host profile 可以启动，taskboard 和 browser bridge 的 HTTP 入口也能响应；这不代表原始远程 commit 已经满足这些条件。本轮用于验证的临时 packaging 提交已经回退。
因此需要区分：

- pnpm 静态 peer 诊断；
- DSH 实际 boot/module fallback；
- 插件自身真正缺少的 runtime dependency。

不能为了消除所有 `pnpm peers check` 输出，就把 DSH Host singleton 包盲目移动到插件的
`dependencies` 中，否则可能制造 Cordis、dsh-tools、dsh-llm 等重复实例。

## 与仓库安装无关、但仍需后续整理的非标准项

这些问题不是 `dsh plugin` 命令造成的，但会影响插件作为标准 package 发布：

- WorkBuddy 的 package version 与源码导出的 version 存在漂移；
- WorkBuddy、lazy-gate、taskboard client 使用 React，但 React/ReactDOM 的 runtime peer
  声明不完整或只存在于 devDependencies；
- taskboard 的部分开发依赖版本仍低于其声明的 DSH compatibility 基线；
- CPA 直接声明部分 DSH Host 包，可能造成 singleton ownership 不清晰；
- image-gen 的 sharp/settings 兼容性以及 slots 注入仍需单独验证；
- browser extension 与 bridge 的 monorepo 构建/发布边界需要明确。

这些项目应分别修 package metadata 或发布流程，而不是通过修改 DSH 核心来绕过。

## 正确的后续执行顺序

1. 保持当前正式 Web profile 不变；
2. 在临时 profile 中使用上游原生命令测试每个 Git package：

   ```bash
   dsh plugin --profile ci add <package-spec>
   ```

3. 只修复不能作为普通 package 安装的仓库：入口文件、`files`、构建产物、sibling link
   和 lifecycle；
4. 再次用同一个 `dsh plugin add github:...` 验证；
5. 确认 Host、client registration、bin/native 前置条件后，才迁移正式 Web profile；
6. 开发时仍使用 `link:`，生产/正常安装使用 registry、tarball 或固定 Git commit。

本文件只记录问题和验证结论，不把当前 profile 强行改成 Git 依赖。
