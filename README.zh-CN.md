# Awesome DSH Plugins

当前 DSH Web profile 中已启用插件的精简清单。以下版本于 2026-09-04
按 DSH `0.1.2-rc.1` 宿主实际安装状态核对。

[English](README.md)

## 已安装插件

| 插件 | 版本 | 类型 | 维护方 / 来源 |
| --- | --- | --- | --- |
| [`@LiuRJ99/dsh-cpa-plugin`](https://github.com/LiuRJ99/dsh-cpa-plugin) | 0.4.0 | 模型供应商 | LiuRJ99 fork，自 [dsh-cliproxyapi-provider](https://github.com/router-for-me/dsh-cliproxyapi-provider) 适配 |
| [`@LiuRJ99/dsh-workbuddy-provider`](https://github.com/LiuRJ99/dsh-workbuddy-provider) | 0.2.1 | 模型供应商 | LiuRJ99 fork |
| [`@yuxianglin/dsh-bridge-browser`](https://github.com/LiuRJ99/dsh-browser) | 0.0.5 | 浏览器控制 | LiuRJ99 fork，自 [dsh-browser](https://github.com/Lum1104/dsh-browser) 适配 |
| [`@zibokapi/dsh-codex-computer-use`](https://github.com/LiuRJ99/dsh-computer-use) | 0.1.2 | Computer Use | LiuRJ99 fork，适配 DSH 0.1.2-rc.1（本地 checkout） |
| [`dsh-better-sidebar`](https://github.com/omdsh-dev/DSH-better-sidebar) | 0.18.0 | Web UI | 作者发布版；本地不维护 fork |
| [`dsh-image-gen`](https://github.com/LiuRJ99/dsh-image-gen) | 0.4.1 | 图片生成 | LiuRJ99 fork，自 [dsh-image-gen](https://github.com/shanliuling/dsh-image-gen) 适配 |
| [`dsh-mobile`](https://github.com/saya-ch/dsh-mobile) | 0.3.8 | 移动访问 | saya-ch 社区版本 |
| [`dsh-sandbox-schema-shim`](https://github.com/xiaohj233/dsh-compat-shims) | 0.1.1 | 兼容层 | 社区 monorepo 子包 |
| [`dsh-spend`](https://github.com/nonewind/dsh-spend) | 0.6.2 | 用量与费用 | nonewind 社区版本 |
| [`dsh-taskboard`](https://github.com/LiuRJ99/dsh-taskboard-cloader) | 0.6.4 | 工作流 | LiuRJ99 fork，自 [cloader/dsh-taskboard](https://github.com/cloader/dsh-taskboard) 适配 |
| [`dsh-tool-lazy-gate`](https://github.com/LiuRJ99/dsh-tool-lazy-gate) | 0.1.0 | 安全与能力控制 | LiuRJ99 fork |

这张表只反映当前实际启用的插件，不是整个生态的插件全集。个人 fork
与上游作者版本分开标注，避免升级时误把上游项目当成本地维护项目。

## 主要能力

- **Taskboard** —— host 权威任务账本与 `taskboard_*` 工具、工作区认领边界、
  按任务执行模型、cron 调度、可选 git worktree 隔离、实时看板、可点击文件链接、
  模型头像、Mermaid 懒加载、会话头部操作，以及可选 Better Sidebar 页签。
  fork 以上游 `0.6.4` 为兼容基线，同时保留这些自主开发的集成。
- **Lazy Gate** —— 会话级高权限工具门控，包含动态限制、执行拦截、Prompt 抑制，
  并接入 taskboard 能力授权。
- **Better Sidebar** —— 作者的 `0.18.0` 右侧栏版本，提供资源管理器、编辑器、
  终端、Git、浏览器等界面，并通过 `ctx.betterSidebar` 为其他插件提供注册服务。
  本机只使用作者发布包，不维护本地 fork。
- **模型与图片** —— CPA、WorkBuddy 注册模型供应商；`dsh-image-gen` 提供基于
  CPA 的图片生成与图库界面。
- **浏览器、电脑与移动访问** —— 浏览器桥接配合扩展提供浏览器工具，Computer Use
  提供 macOS 自动化，DSH Mobile 提供受保护的手机端会话访问。
- **工具插件** —— `dsh-spend` 提供用量与费用视图，sandbox shim 清理模型侧工具
  schema 中多余的沙箱字段。

## 插件关系

当前安装 manifest 中声明了一条强依赖和两条可选的 UI 集成关系：

- `dsh-image-gen` 强依赖 `@LiuRJ99/dsh-cpa-plugin`，版本范围为 `>=0.3.0 <0.5.0`。
- `dsh-taskboard` 可选集成 `dsh-better-sidebar` `^0.18.0`。
- `dsh-image-gen` 同样可选集成 `dsh-better-sidebar` 服务。

taskboard 与 lazy-gate 的关系属于集成契约，不是 npm peer：taskboard 提供能力
元数据，门控插件负责限制对应工具。其余插件彼此独立，或只使用 DSH 宿主服务。

## 兼容性

- 宿主基线：DSH `0.1.2-rc.1`。
- `dsh-taskboard` 声明 `>=0.1.2-rc.1 <0.2.0`，并在 manifest 中将
  `0.1.2-rc.1` 标记为 `compatible`。
- lazy-gate、模型供应商、图片生成、浏览器桥接和 Computer Use 均使用 rc.1 兼容的
  peer 声明。
- 作者 Better Sidebar `0.18.0` 使用 rc.1 兼容的 DSH peer。
- DSH Mobile `0.3.8` 通过 peer 范围声明兼容 `0.1.2` 发布线；已按相同宿主基线核对。

Better Sidebar 特意只使用作者发布版。本仓库不修改、不重新发布，也不维护该项目。

## 安装

通过 DSH 插件命令安装作者发布包：

```bash
dsh plugin --profile web add dsh-better-sidebar@latest
dsh plugin --profile web add dsh-mobile@latest
dsh plugin --profile web add dsh-spend@latest
```

需要 fork 特有功能时，可直接安装 LiuRJ99 的 fork：

```bash
dsh plugin --profile web add github:LiuRJ99/dsh-taskboard-cloader
dsh plugin --profile web add github:LiuRJ99/dsh-tool-lazy-gate
```

调整插件后重启 DSH。安装 `dsh-image-gen` 前先安装 CPA provider，因为图片生成
依赖它提供的服务。

## 维护边界

本清单中需要保留自主开发内容的 LiuRJ99 fork 包括：CPA、WorkBuddy、浏览器桥接、
图片生成、taskboard 和 lazy-gate。`dsh-better-sidebar` 不在此列：只使用作者发布包，
不代表本地维护或修改上游项目。

## 许可证

本清单为 MIT。各插件保留上游许可证：taskboard 与 DSH Mobile 为 Apache-2.0，
其他列出的插件为 MIT（如上游项目另有声明，以其声明为准）。
