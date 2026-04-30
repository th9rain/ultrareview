# ultrareview

在你自己的环境里，复刻 Claude Code 风格的多 Agent 并行代码审查工作流。

`ultrareview` 是一个可复用 Skill，核心是把高质量代码审查流程产品化：并行派发多个专长不同的审查 Agent，让它们互相质疑彼此的发现，最后将确认过的问题汇总成一份结构化报告。

## 为什么做这个仓库

Claude Code 更高级的代码审查能力，本质上绑定在官方托管 / 付费审查能力上。这个仓库**并不包含** Anthropic 私有的审查后端。

它做的事情是：把 ultrareview 背后的**工作流模式**复刻出来，让你可以在自己的 agent 运行环境、自己的模型供应商、自己的基础设施上，跑出一套非常接近的多 Agent 审查流程。

这意味着：
- 不依赖 Anthropic 官方托管的 ultrareview 产品
- 不必把代码交给一个你无法控制的第三方审查服务
- 不需要承担原产品那种固定的托管审查费用
- 你的真实成本，变成你自己模型 / 基础设施的使用成本

如果要一句话概括：

> 一个自托管的 ultrareview 风格工作流，不需要购买 Claude Code 官方托管审查产品。

## 它具体做什么

`ultrareview` 会把代码审查变成一个多 Agent 系统：

1. **收集上下文**
   - 文件、目录、PR diff 或整个仓库
   - `CLAUDE.md` / `REVIEW.md` 中的项目规则

2. **并行派发专项 Agent**
   常见角色包括：
   - Logic Verifier
   - Security Sentinel
   - Performance Oracle
   - Boundary Inspector
   - Architecture Reviewer

3. **交叉验证问题**
   - Agent 会尝试证伪彼此的结论
   - 没有被确认的问题会被丢弃
   - 重复发现会被合并去重

4. **输出结构化报告**
   - 🔴 Critical
   - 🟡 Warning
   - 🟣 Pre-existing

## 为什么它比单轮 review prompt 更强

单轮 prompt 式代码审查虽然方便，但经常会混在一起：
- 覆盖浅
- 严重级别不稳定
- 重复问题多
- 误报偏高

`ultrareview` 的提升来自四件事：
- **角色分工**：每个 Agent 从不同角度审
- **并行审查**：同一批改动被多视角同时检查
- **交叉质疑**：结论要先经得起其他 Agent 反驳
- **统一汇总**：最后报告会去重并做严重级别排序

所以它更适合：
- Bug Hunting
- 安全导向审查
- 大型 diff
- 高风险重构
- 重要改动的合并前检查

## 来源

这个 Skill 基于对 Claude Code 多 Agent 审查模式的逆向分析与复刻整理，再适配成一个可复用的 OpenClaw Skill 包。

真正值得迁移的，不只是“让一个强模型看代码”，而是背后的系统设计：

> 价值不在单个模型，而在系统工程：多审查员、角色分工、交叉证伪、统一汇总。

## “免费使用 ultrareview”到底是什么意思

很多人会把它概括成“免费使用 Claude Code 的 ultrareview”。

这个说法方向上能理解，但技术上并不精确。

这个仓库真正提供的是：
- 一套**自托管的 ultrareview 风格工作流复刻**
- **不需要购买 Anthropic 官方托管审查产品**
- 但底层依然使用**你自己的模型 / API 配额和运行资源**

所以更准确的表述是：

> 你可以在不购买官方托管产品的前提下，获得 ultrareview 风格的多 Agent 审查能力。

而不是：
- “这里面有 Anthropic 的私有后端”
- “模型调用完全免费”
- “直接解锁官方同款隐藏功能”

## 仓库结构

```text
ultrareview/
├── README.md
├── README.zh-CN.md
├── SKILL.md
└── references/
    ├── architecture.md
    └── diy-setup.md
```

## 文件说明

- `SKILL.md`：主 Skill 定义，包含审查流程、报告格式和落地方案入口
- `references/architecture.md`：多 Agent 审查架构说明，包括角色分工、交叉验证与效果数据
- `references/diy-setup.md`：实操指南，覆盖 Agent Teams、自定义 subagents、插件方案和 CI/CD 集成

## 4 种落地方式

这个仓库里整理了四种比较实用的接入路径：

1. **原生 Agent Teams**
   适合支持团队式并行 agent 的 coding agent。

2. **插件式方案**
   适合已经接入 compound orchestration 之类插件的环境。

3. **自定义 subagents**
   适合想完全控制角色、prompt 和汇总逻辑的场景。

4. **CI/CD 集成**
   适合做 PR 自动审查流水线。

## 典型使用方式

典型触发语句：
- “帮我对 `src/auth/` 跑一下 ultrareview”
- “用逻辑、安全、性能、边界条件几个 agent 审一下这个 PR diff”
- “对这次重构做一次深度合并前审查”
- “对这些改动做一次多 Agent 的安全和正确性审计”

典型适用场景：
- 深度代码审查
- 合并前缺陷扫描
- Bug Hunting
- 安全审计
- 大 PR 分析
- 高风险生产改动检查

## 这个 Skill 包装的重点

这个仓库重点提供的是可复用部分：
- 审查流程
- Agent 角色拆分
- 交叉验证模式
- 报告结构
- 多种落地路径

它刻意保持轻量和可迁移，方便你改造成：
- 本地 CLI 工作流
- OpenClaw / skill 系统
- 自托管 coding-agent 环境
- GitHub Actions 或其他 CI 流水线

## 延伸阅读

如果你想看更底层的技术背景，可以继续读：
- `references/architecture.md`
- `references/diy-setup.md`

## License

发布前请把占位 `LICENSE` 文件替换成你真正要采用的开源许可证。
