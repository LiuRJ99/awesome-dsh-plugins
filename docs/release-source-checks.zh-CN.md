# 发布来源与上游检查

目标来源由 README 的安装列及 compatibility JSON 管理；本机已装状态应现场查询，不写进公开目录。
核对时分别报告“已发布固定来源”“已安装目录目标”和“已吸收上游”三项，不能用同一个版本号代替全部结论。

## 固定来源是否已发布

GitHub Release 不等于 npm 发布，tag 不等于带可运行入口的安装包。按实际交付类型核对：

- Git 来源：远端 commit 存在，tag 解引用后等于目录 SHA，package 版本正确，运行入口包含在该快照中。
- Release tarball：Release 非 draft、asset 名称和 SHA-256 与目录一致，安装包带真实运行入口；下载到稳定本地路径再安装。
- registry：目录精确版本可下载，检查来源仓库、包名和 integrity；不要裸装重名 fork。
- 构建型 Browser：bridge tarball 和 Chrome 扩展都要核对；workspace、bridge、manifest 版本分别记录。

例如，检查目录指定的 Release，使用已有安全认证的 `gh`，无需输出 token：

```bash
gh release view <目录指定的-tag> --repo <owner/repo> \
  --json tagName,isDraft,isPrerelease,publishedAt,assets,url
gh api repos/<owner/repo>/git/ref/tags/<目录指定的-tag>
# object.type 为 tag 时，再读取 git/tags/<object.sha>，直到解析为 commit。
npm view <registry-package>@<目录精确版本> version dist.integrity
```

比较公开新版时另查 `gh release list --repo <owner/repo>` 或 registry 的 `dist-tags`，不要把 `main` 的 package 版本当成已发布版本。
新版发现只记录发布元数据；有 open sync PR 的 fork 仍按冻结 PR 评估，不用较新的 Release 替换其快照。

Record/Replay `0.3.3-dev.1` 当前目录交付为公开精确 commit
`277a05b527ccfaf8e555933209e70886bf1e545d`，不是该版本的 GitHub Release。
该 commit 可复现并可安装，不能汇报成“所有适配版本均已有 Release”。需要改为 release tag 时，先构建、测试、打包并发布，再同时更新 README 和 JSON。
GitHub MCP 同样使用固定 commit，因为来源仓库未提供对应 tag/Release。

Computer Use `v0.1.6-dev.2` 的 GitHub Release 与 tarball 已提供；目标提交的 CI 通过，但其 Release workflow 的 npm 发布步骤报 `ENEEDAUTH`，对应版本不在 npm registry。
目录使用 GitHub 固定来源，不依赖这次 npm 发布。检查发布时应分别报告安装物料可用与自动发布流程的结果，不能把它们合并成一个“通过”。

可选 Laya SDK 的目标为 registry `@receptron/laya@0.1.1`；它不是 DSH 插件。
比较 registry 新版本后仍须验证 Decision Provider API、模型物料和约束质量，不能仅因 npm `latest` 变化就替换目标。
Mobile 也是可选功能，检查它的新版本不意味着需要在 Desktop-only 部署中安装。

## fork 先查询同步 PR

在 fork 的默认分支上查询 `sync/upstream-main`：

```bash
gh repo view <fork-owner/repo> --json defaultBranchRef
gh pr list --repo <fork-owner/repo> --base <默认分支> \
  --head sync/upstream-main --state open
gh pr view <编号> --repo <fork-owner/repo> --json body,commits,files,comments,reviews
# 文件多时分页读取完整列表，避免只审截断摘要。
gh api --paginate repos/<fork-owner/repo>/pulls/<编号>/files
gh api repos/<fork-owner/repo>/actions/workflows/sync-upstream.yml
gh run list --repo <fork-owner/repo> --workflow sync-upstream.yml --limit 3
```

有 open PR 时审其正文、完整 commits/files 和决策评论；它是不可变的上游快照。
正文的新增评估范围与整个 PR 的历史 fork delta 可能不同，不能把所有 diff 都当作本轮新功能。
保留 fork workflow 的单独提交也可能让 PR head 不等于正文 `upstream-sha`，应核对该提交的用途。
不要在评估前做本地 fetch/diff/试合并，也不要因为上游继续前进而重写当前快照。

同步 workflow 存在且运行成功，只证明检测流程执行了，不证明有冲突的上游功能已经合并。
无 PR 时先核对 workflow 和既有决策边界；只有流程失效、缺失或明确重新评估旧结论时再手工比对。
同步 workflow 的评估边界及 `whole` / `partial` / `rejected` 评论用于避免重复评估；实际发评论或关闭 PR 仍须有用户授权。

上游 peer 范围不覆盖目标 Host 时，不直接安装。使用 `includePrerelease: true` 比较 `@deepseek-ai/dsh-*`；不要把 Cordis / Schemastery 的独立版本线混入。
源码或配置冲突需要逐功能评估，生成物和 lockfile 冲突单独处理。选择性吸收后保留 fork 的兼容声明、来源规则、门禁和 provider 集成，再进行 candidate 验证。

## 自维护插件与实际安装

对无 upstream 的自维护仓库，核对 main 与目标 tag 的 commit：未发布的源码改动和仅 CI / 文档改动分开报告。
检查 Release asset 与相关 CI；不要因为 main 有一个 workflow 提交就认定需要新的功能版本。

实际安装通过 Plugin Manager、profile manifest 和 lockfile 核对；CLI/Web 可另用：

```bash
dsh --profile <profile> --dump-config
```

Desktop 必须检查自己的 Host 与 profile，不能拿另一份 CLI profile 的结果代替。
新版本先验证兼容性、物料、加载和相关功能，再迁移正式部署；检查版本本身不授权合并、升级或发布。

## 本轮已记录的选择性吸收

以下同步快照采用 `partial`，关闭 PR 以记录完整评估边界；不是整包合并上游。边界之后的新提交仍由下一轮同步 PR 评估。

| 同步 PR | 已吸收 | 暂缓 / 保留 | 对应 fork Release |
| --- | --- | --- | --- |
| [Browser #5](https://github.com/LiuRJ99/dsh-browser/pull/5) | `6d6ce252` 后台开页参数、审批提示、前台/受控标签分离 | 保留 DSH 0.2 peers、独立会话与权限限制 | `v0.1.13-dev.1` |
| [Taskboard #5](https://github.com/LiuRJ99/dsh-taskboard-cloader/pull/5) | `930bb2c6` / `5746284b` 持久化队列、原子交接、串行派发、停止等待；默认一秒间隔 | 暂缓自动后继卡片、runAt 重设计、批量永久删除；保留周期审核卡片及 Sidebar 集成 | `v0.7.7` |
| [ImageGen #5](https://github.com/LiuRJ99/dsh-image-gen/pull/5) | `d9a58cd9` 保存失败结果传播与 UI 保存重试 | 保留 CPA、事务/删除标记/收藏/工作区元数据；暂缓 studio、OAuth、批量 ZIP | `v0.5.10` |

源码测试及 Desktop candidate 集成证据见 [Desktop 说明](desktop-0.2.0-rc.2.zh-CN.md#选择性上游修复)。Chrome 重载、标签控制审批与模型/权限检查仍须在部署机器实测，不能把源码通过等同于已完成真人浏览器验收。
