# 本地拉取与验证提示词

将下面内容交给本地开发 Agent。详细依据见 [DSH 0.2.0-rc.2 验证说明](dsh-0.2.0-rc.2.zh-CN.md)。

## CLI/Web 基础验证

```text
请拉取 LiuRJ99/awesome-dsh-plugins 以及 LiuRJ99/dsh-browser、dsh-computer-use、dsh-record-replay、dsh-spend 的 main，并完成本地官方 DeepSeek Harness 插件验证。

先定位各仓库并检查未提交改动；已有 main checkout 使用 git pull --ff-only，缺少的再 clone，不要 reset、clean 或丢弃已有改动。若要评估 fork 上游，先查询 open sync PR，按 PR-first 规则审查，不先做本地 fetch/diff/试合并。
读取 awesome-dsh-plugins/README.zh-CN.md、docs/dsh-0.2.0-rc.2.zh-CN.md、compatibility/dsh-0.2.0-rc.2.json 及各仓库 AGENTS.md，按固定提交核对物料。

使用官方 @deepseek-ai/dsh@0.2.0-rc.2 和 Node 24，在独立运行时、独立 DSH_HOME 和 candidate-0.2 profile 中安装；保留现有 Web/Desktop profile。Desktop 另外核对实际 Host runtime，不要假定与 CLI 一致。不要自动升级到 alpha 或其他版本。
按文档构建、测试并打包四个适配插件，Browser 同时加载 Chrome 扩展。通常使用 pnpm 11.7.0；Computer Use 源码使用固定 pnpm 11.21.0。Browser smoke 必须通过 DSH_TEST_CLI 指向完整官方 npm Host，Record/Replay 使用 link-dsh.mjs 链接该 Host。
其他插件按目录的精确版本/提交/校验和安装，provider 先于消费者。CPA 的官方依赖 overrides 只合并到新 candidate profile，不覆盖已有字段，不改 node_modules、bundle 列表，不使用 compatibility version exemption。

先完成 Plugin Manager、加载、资源请求及源码测试，再验证 Browser、Sidebar、Taskboard、ImageGen Gallery 和 Lazy Gate。Desktop-only 时跳过 Mobile；需要移动访问时另行验证。有本地可用凭据或服务时，继续验证模型、图片、GitHub MCP、Decision Engine 和 Spend 真实用量；不要把密钥或访问 token 打印或提交。
macOS 上按文档构建 Computer Use 和固定 v0.1.1 的 open-record-replay native，并由用户完成系统权限。Linux/Windows 将 macOS 原生验证标为不适用。录制器须由用户手动输入 /open-record-replay 解锁，不要自行启动录制。
Taskboard v0.7.7 / ImageGen v0.5.10 已修复 Sidebar 0.24.1 的可选 peer 并完成相关 UI 集成测试；部署时复核入口与 UI，不要仅放宽版本范围掩盖新问题。
Desktop 另读 docs/desktop-0.2.0-rc.2.zh-CN.md，使用自身 Plugin Manager；Chrome 扩展版本与 bridge 分别核对，实际 Host 端口不在自动发现列表时手动配置桥地址。原生 helper 的两类权限仍须保留。

发现问题先定位到实际仓库，完成必要修复及相关测试；未经我另行授权，不推送、不发布、不迁移到正式 profile。最终用表格列出每个插件的实际版本、Host 版本、加载状态、测试结果、功能验证、错误/警告和后续动作，明确区分通过、失败、跳过、未测。
```

## Desktop-only 验证

```text
请按 awesome-dsh-plugins/docs/desktop-0.2.0-rc.2.zh-CN.md 验证官方 DeepSeek Harness Desktop，目标 Host 为 0.2.0-rc.2、Node 24。先现场核对 Desktop 内的实际 Host、Home 与 profile，不以另一份 CLI 的版本或安装结果代替。

读取 README.zh-CN.md、兼容 JSON、发布来源核对指南与适用 AGENTS.md。先检查已有仓库改动，保留工作区；评估 fork 时先查 open sync PR，审完整 commits/files 和决策评论，不先做本地 fetch/diff/试合并。按目录精确来源核对远端 tag/commit、Release asset checksum 与已装 lockfile。

使用独立 Home 验证 Desktop candidate；通过后才修改正式 profile，安装使用 Desktop 自身 Plugin Manager。保留现有会话、任务、附件、Gallery、凭据和配置，不复制 node_modules，不改签官方应用，不手工修改运行物料。CPA overrides 合并并保留已有字段。

只保留官方应用入口，不另外安装独立 Web 或 Mobile，也不创建额外浏览器启动器。日常 Chrome 中分别核对已加载扩展的 manifest 和 bridge 版本；在扩展内按实际 Desktop Host 端口配置桥地址，检查连接、标签页查询和用户批准后的页面操作。保留实际 provider 引用的 Computer Use helper；native 构建、Desktop Host preflight 和系统权限逐项验收，不代替用户批准。录制只在用户手动输入 /open-record-replay 后进行。

先验证加载、运行入口、依赖解析和源码测试，再检查 Sidebar 的 Taskboard/Gallery 集成、Lazy Gate 资源及会话状态。有已有本地服务/凭据时验证真实模型、图片生成/编辑、Spend、GitHub 文件读取和决策建议，不打印秘密，不使用版本豁免。决策仅建议模式显式设置 advice-only；质量样例失败、旧历史格式与执行模式未测分别记录。

发现问题先定位并完成范围内修复与相关测试；未经另行授权不推送、不发布、不升级正式 Host/插件、不合并上游。最终按“完全可用、需调整、不兼容”分类，用表格报告版本、来源、发布状态、Host、加载、功能、错误/警告和后续动作，并区分通过、失败、跳过、未测。本机安装清单只放私有报告，不写入公开目录。
```
