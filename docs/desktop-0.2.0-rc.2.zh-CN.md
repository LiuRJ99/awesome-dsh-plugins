# 官方 Desktop 0.2.0-rc.2 部署与验收

适用目标为官方 DeepSeek Harness Desktop 的 Host `0.2.0-rc.2`、Node 24。
这是该组合的部署说明与验证范围，不是某台机器的安装清单。
不同 Desktop 版本必须重新检查实际 Host、插件 peers 和运行入口；CLI 的版本不能代替 Desktop 的版本。

## 安装与数据目录

先在独立 `DSH_HOME` 中验证 Desktop candidate，再部署到正式 Desktop。使用该 Desktop 版本支持的启动方式设置 Home；从 Finder 启动的应用不能假定继承终端中的 `export DSH_HOME`。
在 Plugin Manager 核对实际 profile 和 Host runtime。不要改签官方应用，也不要修改应用内的 Host 或 profile 的 `node_modules`。

Desktop 的插件安装使用自身 Plugin Manager；[CLI/Web 示例](dsh-0.2.0-rc.2.zh-CN.md#安装与启动)针对 CLI profile，不能直接当成 Desktop 的安装命令。
使用 [目录固定来源](../README.zh-CN.md#插件目录)，先安装 CPA 等 provider，再安装消费者。CPA 的精确 overrides 仍须按[候选依赖说明](dsh-0.2.0-rc.2.zh-CN.md#候选-profile-依赖解析)合并，不能覆盖已有配置或使用版本豁免。

迁移前确认 Web 与 Desktop 实际使用的 Home 和 profile，分别备份会话、附件、Taskboard、Gallery 与配置。
不要复制整份 `node_modules`，也不要用旧日志解析失败作为删除原文件的理由。
历史日志能否打开、会话标题、任务归属和图片资源都应逐项核对；文件存在不等于迁移通过。

## Chrome 直接连接

Browser 仍由两个部分组成：Desktop 内的 bridge 和 Chrome 中的扩展。
可以在日常 Chrome 使用扩展，不需要为连接额外安装一个 DSH 浏览器启动器，也不需要先发启动浏览器的会话指令。

目录 Release `v0.1.13-dev.1` 包含 bridge `0.0.13-dev.1` 和 manifest `0.1.12` 的 Chrome 扩展。
这三个版本号各有含义，安装 bridge 不会自动更新已经加载到 Chrome 的另一份扩展。
在 `chrome://extensions` 核对版本与加载目录；使用同一固定 Release 的扩展包，避免仅确认 bridge 版本。

扩展“桥地址”留空时查找 `3080 / 3081 / 3090 / 14389 / 43189`。
如果实际 Desktop Host 端口不在列表中，在扩展设置里填写一次：

```text
ws://127.0.0.1:<实际 Desktop Host 端口>/ext/bridge
```

实际端口应从 Desktop 的连接信息或 Host 启动配置核对，不能沿用旧 Web 端口。
Chrome 本机 loopback 连接无需在设置中填写 token；Firefox 和远程连接仍按扩展文档配置 token，不要为便利关闭认证或扩大监听范围。
打开 Desktop 和 Chrome 后检查连接状态，再验证标签页查询和用户批准后的页面操作。
连接成功不等于已授权控制：`/browser` 会话门禁与扩展的标签页/应用审批仍需遵守。

## macOS 原生辅助组件

`dsh-computer-daemon` 是 Computer Use 的辅助组件，可以同时出现在“录屏与系统录音”和“辅助功能”权限列表中；这不是两个客户端。
只要实际启用的 computer provider 仍引用它，迁移到 Desktop 后也必须保留它及所需权限。
DeepSeek Harness 自身获得权限，不能替代另一个 helper 的 TCC 授权。

Computer Use 更换版本后，用已安装包自带的 setup CLI 重建 helper；Record/Replay 使用目录固定的 recorder checkout 构建 native。
权限取决于 bundle id、签名、路径及实际启动方式。新 candidate 的 Host preflight 必须单独核对；终端里的权限检查通过不能替代它。
录制始终等待用户手动输入 `/open-record-replay` 解锁，不能由模型代为授权。

## 已验证范围与已知限制

下表记录目标组合的验收证据，不保证其他机器、凭据或模型输入同样通过。

| 功能 | 结果 | 范围与限制 |
| --- | --- | --- |
| 模型供应商与真实文本调用 | 通过 | CPA、WorkBuddy；凭据与本地 bridge 仍需部署时核对 |
| Taskboard / Gallery / Sidebar | 通过 | Taskboard `0.7.7`、ImageGen `0.5.10` 与 Sidebar `0.24.1` 的入口及 UI 集成；旧可选 peer 问题已解决 |
| Browser | 通过 | bridge 连接、标签页查询；实际页面操作由用户确认；Firefox 未测 |
| 图片生成与编辑 | 通过 | 包含保存和 Gallery，实际生成/编辑由用户确认；不保证所有 provider 的每次调用均成功 |
| Computer Use | 通过 | 用户批准后的真实输入与点击；没有绕过权限，隔离 candidate 的权限失败不能算通过 |
| Record/Replay | 通过（用户确认） | 用户手动解锁后实测；没有由代理再次发起录制 |
| Spend | 通过 | 真实用量与视图；费用为估算 |
| Lazy Gate `0.1.7` | 通过 | 官方 Host 首轮工具目录、同轮用户解锁、设置页资源与会话状态 |
| Decision Engine `0.4.19` | 通过（仅建议） | 显式设置 `executionMode: advice-only`；执行模式未验收 |
| Laya `0.1.1` | 需调整 | SDK/推理可用；四个约束质量样例中一个失败，不能外推为自动执行安全；`0.1.2` 未作本轮质量验收 |
| GitHub MCP | 部分通过 | REST `github_file_read` 可用；官方 Host 的 embedded resource 正文呈现失败，仍需 Host 适配 |
| Sandbox shim `0.1.1` | 通过 | `workspace-write` / `read-only` 不改 schema；已有 `danger-full-access` 下仅去除冗余字段，不提升权限 |
| Mobile | 跳过 | 可选功能，Desktop-only 部署不需要安装；保留目录中的固定版本供另行验证 |
| 旧历史格式 | 部分不兼容 | 旧 descriptor / `config.speed` 可被官方 Host 解码器拒绝；保留原文件，等待受支持的迁移方案 |

仍可能出现 Host 外供依赖和预发布版本的 pnpm peer 提示。应分别检查运行时解析与实际加载，不要使用版本豁免把警告隐藏起来。
Sidebar 的终端进程可能在 Host 重启后结束，需要新建终端；插件显示 active 本身不能证明已有终端仍在运行。

## 选择性上游修复

Browser `v0.1.13-dev.1` 提供后台开页参数，保留审批、前台焦点和独立会话控制目标。Taskboard `v0.7.7` 提供持久化到期窗口、串行 FIFO 派发、重启恢复与默认一秒派发间隔；没有引入自动后继卡片或批量永久删除。ImageGen `v0.5.10` 提供图库保存失败提示与保存重试，重试不会重新调用模型。

源码验证分别通过 540、439、148 项；Taskboard 另有 2 项 opt-in Git 测试未运行。类型检查、构建与包入口检查通过。桌面 candidate 的两张只读任务按计划触发并获得真实模型回复，Sidebar 同时挂载 Taskboard 与 Gallery。故障注入下的 IndexedDB/页面保存重试由源码测试验证；没有据此声称已在正式图库制造保存故障或重新测试全部图片 provider。

## 与 Web 的差异

Desktop 提供原生目录选择、系统应用和配套运行时入口；看板、Gallery、费用和 Browser 的 UI 仍使用 Host 客户端服务。
内部本机 HTTP / Browser bridge 是 Desktop 功能的一部分，卸载独立 Web 不应把它们一并删除。
只保留官方应用入口与正式 Home；旧启动器和不再使用的测试浏览器 profile 可先归档，删除前核对扩展路径、helper 路径及插件引用。

发布与上游检查方法见[发布来源核对](release-source-checks.zh-CN.md)。
