# ultrareview

> 在你自己的环境里，复刻 Claude Code 风格的多 Agent 并行代码审查工作流。

`ultrareview` 是一个可复用 Skill，核心是把高质量代码审查流程产品化：并行派发多个专长不同的审查 Agent，让它们互相质疑彼此的发现，最后将确认过的问题汇总成一份结构化报告。

## 为什么这东西值得做

现在很多 AI 代码审查本质上还是：
- 一个模型
- 一轮输出
- 一堆混在一起的评论

能用，但常见问题也很明显：
- 覆盖浅
- 重复发现多
- 严重级别不稳定
- 误报偏高

`ultrareview` 采用的是另一种模式：
- **专项审查员**，而不是一个泛化 reviewer
- **并行审查**，而不是串行思考
- **交叉验证**，而不是第一眼觉得像 bug 就直接报
- **统一汇总**，而不是把原始笔记直接扔给你

所以它更适合：
- 重要改动的合并前审查
- Bug Hunting
- 安全导向审查
- 大型 diff
- 高风险重构
- 更关注正确性而不是样式建议的代码库

## 这个仓库到底是什么

这个仓库**不包含** Anthropic 私有的托管审查后端。

它做的事情是：把 ultrareview 背后的**工作流模式**复刻出来，让你可以在自己的：
- agent 运行环境
- 模型供应商
- 本地或自有基础设施

上跑出一套接近的多 Agent 审查流程。

所以更准确的价值主张是：

> 一个自托管的 ultrareview 风格工作流，不需要购买 Claude Code 官方托管审查产品。

实际含义是：
- **不需要** Anthropic 官方 hosted ultrareview 服务
- **不需要**把代码交给你无法控制的第三方审查系统
- **仍然需要**你自己的模型 / API / 基础设施成本

所以这不是“推理白嫖”。
而是“自带 runtime，复刻 workflow，绕开 hosted product fee”。

## 核心工作流

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

## Quick Start

### 方案 1：直接使用 Skill 包

把这个仓库放进你的 skill 目录，或者直接复用下面这些文件：

```text
ultrareview/
├── SKILL.md
└── references/
    ├── architecture.md
    └── diy-setup.md
```

然后在这些场景触发：
- 深度代码审查
- 安全审计
- 大 PR review
- 正确性优先的审查

示例 prompt：

```text
Run ultrareview on src/auth/
Review this PR diff with logic, security, performance, and edge-case agents
Do a deep pre-merge audit for this refactor
```

### 方案 2：改造成你自己的 coding agent 工作流

如果你的环境支持：
- subagents
- team / parallel agent execution
- slash command prompt files
- CI headless CLI review

那就可以直接把下面这些内容接进去：
- `SKILL.md`
- `references/diy-setup.md`

## 示例输出

```markdown
# Ultrareview Report
**Scope**: `src/auth/`, `src/session.ts`
**Agents**: 5 | **Duration**: 2m 41s | **Findings**: 4

## 🔴 Critical (1)
### [C1] Session token can be reused after logout
- **Location**: `src/session.ts:88-121`
- **Issue**: Logout clears client state but does not revoke the persisted server-side token.
- **Root cause**: Token invalidation path is missing in the logout flow.
- **Fix**: Revoke the token on logout and reject stale tokens during session validation.

## 🟡 Warning (2)
### [W1] User lookup can trigger N+1 queries during permission checks
- **Location**: `src/auth/permissions.ts:41-79`
- **Issue**: Role expansion performs repeated DB reads inside a loop.
- **Fix**: Batch-load role mappings before permission evaluation.

### [W2] Null email path produces silent fallback behavior
- **Location**: `src/auth/user.ts:24-37`
- **Issue**: Missing email falls through to guest behavior without explicit handling.
- **Fix**: Fail closed or return an explicit typed error.

## 🟣 Pre-existing (1)
### [P1] Legacy password hash path lacks migration guard
- **Location**: `src/auth/hash.ts:12-33`
- **Issue**: Old hash format is still accepted without progressive upgrade.
```

## 仓库结构

```text
ultrareview/
├── README.md
├── README.zh-CN.md
├── SKILL.md
├── LICENSE
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

## “免费使用 ultrareview”到底是什么意思

很多人会把它概括成“免费使用 Claude Code 的 ultrareview”。

这个说法能理解，但不够准。

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

## 推荐 GitHub 元信息

**仓库描述**

> Recreate Claude Code–style multi-agent ultrareview with a self-hosted skill workflow.

**建议 topics**

```text
code-review
multi-agent
ai-code-review
claude-code
agent-workflow
openclaw
prompt-engineering
security-review
static-analysis
```

## 来源

这个 Skill 基于对 Claude Code 多 Agent 审查模式的逆向分析与复刻整理，再适配成一个可复用的 OpenClaw Skill 包。

真正值得迁移的，不只是“让一个强模型看代码”，而是背后的系统设计：

> 价值不在单个模型，而在系统工程：多审查员、角色分工、交叉证伪、统一汇总。

## 延伸阅读

- `references/architecture.md`
- `references/diy-setup.md`
- `README.md`

## License

MIT
