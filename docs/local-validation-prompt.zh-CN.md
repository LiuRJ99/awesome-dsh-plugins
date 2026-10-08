# 本地拉取与验证提示词

将下面内容交给本地开发 Agent。详细依据见 [DSH 0.2.0-rc.2 验证说明](dsh-0.2.0-rc.2.zh-CN.md)。

```text
请拉取 LiuRJ99/awesome-dsh-plugins 以及 LiuRJ99/dsh-browser、dsh-computer-use、dsh-record-replay、dsh-spend 的 main，并完成本地官方 DeepSeek Harness 插件验证。

先定位各仓库并检查未提交改动；已有仓库使用 git fetch 和 git pull --ff-only，缺少的再 clone，不要 reset、clean 或丢弃已有改动。
读取 awesome-dsh-plugins/README.zh-CN.md、docs/dsh-0.2.0-rc.2.zh-CN.md、compatibility/dsh-0.2.0-rc.2.json 及各仓库 AGENTS.md，按固定提交核对物料。

使用官方 @deepseek-ai/dsh@0.2.0-rc.2 和 Node 24，在独立运行时、独立 DSH_HOME 和 candidate-0.2 profile 中安装；保留现有 Web/Desktop profile。Desktop 另外核对实际 Host runtime，不要假定与 CLI 一致。不要自动升级到 alpha 或其他版本。
按文档构建、测试并打包四个适配插件，Browser 同时加载 Chrome 扩展。通常使用 pnpm 11.7.0；Computer Use 源码使用固定 pnpm 11.21.0。Browser smoke 必须通过 DSH_TEST_CLI 指向完整官方 npm Host，Record/Replay 使用 link-dsh.mjs 链接该 Host。
其他插件按目录的精确版本/提交/校验和安装，provider 先于消费者。CPA 的官方依赖 overrides 只合并到新 candidate profile，不覆盖已有字段，不改 node_modules、bundle 列表，不使用 compatibility version exemption。

先完成 Plugin Manager、加载、资源请求及源码测试，再验证 Browser、Sidebar、Taskboard、ImageGen Gallery、Lazy Gate 和 Mobile。有本地可用凭据或服务时，继续验证模型、图片、GitHub MCP、Decision Engine 和 Spend 真实用量；不要把密钥或访问 token 打印或提交。
macOS 上按文档构建 Computer Use 和固定 v0.1.1 的 open-record-replay native，并由用户完成系统权限。Linux/Windows 将 macOS 原生验证标为不适用。录制器须由用户手动输入 /open-record-replay 解锁，不要自行启动录制。
Taskboard/ImageGen 的可选 Sidebar peer ^0.21.1 与 Sidebar 0.24.1 警告尚未解决，请重点验证 UI，不要仅放宽版本范围掩盖问题。

发现问题先定位到实际仓库，完成必要修复及相关测试；未经我另行授权，不推送、不发布、不迁移到正式 profile。最终用表格列出每个插件的实际版本、Host 版本、加载状态、测试结果、功能验证、错误/警告和后续动作，明确区分通过、失败、跳过、未测。
```
