# ultrareview

一个可复用的多 Agent 代码审查 Skill，灵感来自 Claude Code 隐藏的 `/ultrareview` 工作流。

这个仓库把它的核心思路封装成了一个可迁移的 Skill：并行派发多个专长不同的审查 Agent，让它们交叉验证彼此的发现，最后输出一份按严重级别整理过的结构化问题报告。

## 这个仓库是干什么的

Claude Code 自带的高级代码审查能力，本质上绑定在官方托管 / 付费能力上。这个项目的目标不是复刻 Anthropic 的私有云后端，而是把它背后的**工作流模式**提炼出来，做成一个可以在你自己环境里跑的 Skill。

实际意义就是：
- **不需要**开通 Anthropic 官方托管的付费 ultrareview 能力
- 可以在你自己的 agent 运行环境里复现非常接近的多 Agent 审查流程
- 代码可以留在你自己的工作区或基础设施内
- 成本变成你自己的模型/API 调用成本，而不是官方托管审查的固定收费

## 它能做到什么

`ultrareview` 会把代码审查拆成一个多 Agent 协作系统：

1. **收集上下文**
   - 审查范围：文件、目录、PR diff 或整个仓库
   - 项目规则：`CLAUDE.md` / `REVIEW.md`

2. **并行派发审查 Agent**
   常见角色包括：
   - Logic Verifier（逻辑正确性）
   - Security Sentinel（安全漏洞）
   - Performance Oracle（性能问题）
   - Boundary Inspector（边界条件）
   - Architecture Reviewer（架构设计）

3. **交叉验证发现**
   - Agent 之间互相质疑和复核
   - 没有证据支撑的问题会被丢弃
   - 重复发现会被合并

4. **生成结构化报告**
   - 🔴 Critical
   - 🟡 Warning
   - 🟣 Pre-existing

## 你真正得到的价值

相比“单轮 prompt 式代码审查”，这个 Skill 更强调：
- 用专长分工提升问题覆盖率
- 用交叉验证降低误报率
- 用严重级别 + 根因分析提升可执行性
- 把一次性的审查 prompt 变成可复用、可迁移、可自动化的工作流

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

- `SKILL.md`：主 Skill 定义，包含审查流程、输出格式、适用场景和落地方案入口
- `references/architecture.md`：多 Agent 审查架构说明，包括角色分工、交叉验证机制、效果与成本对比
- `references/diy-setup.md`：落地指南，覆盖 Agent Teams、自定义 subagents、插件式集成、CI/CD 接入

## 怎么用

把这个 Skill 安装或复制到你的技能目录后，就可以在以下场景触发：
- 深度代码审查
- 合并前缺陷扫描
- Bug Hunting
- 安全审计
- 大 diff 的多 Agent Review

典型触发语句：
- “帮我对 `src/auth/` 跑一下 ultrareview”
- “对这个 PR diff 做一次多 Agent 审查”
- “从逻辑、安全、性能、边界条件几个角度审一下这些改动”

## 4 种落地方式

这个仓库里给了四种比较实用的实现路径：

1. **原生 Agent Teams**
   如果你的 coding agent 支持团队式并行 agent，这是最直接的方式。

2. **插件式方案**
   如果你已经在用 compound / orchestration 类插件，可以直接接进去。

3. **自定义 subagents**
   适合想完全控制角色定义、prompt 和汇总逻辑的场景。

4. **CI/CD 集成**
   适合把多 Agent 审查接到 PR 自动化流程里。

## 关于“免费使用 Claude Code 的 ultrareview 功能”这件事

更准确的说法不是“直接白嫖官方功能”，而是：

> **用自托管 Skill 的方式，复现 Claude Code 官方 ultrareview 的工作流能力。**

也就是说：
- 你绕开的是 **Anthropic 官方托管审查产品的收费模式**
- 你获得的是 **同类多 Agent 审查能力**
- 但底层模型调用、Agent 运行、CI 资源这些成本，仍然由你自己的环境承担

如果要写成一句适合 README 的表述，我建议这样写：

> 在你自己的运行环境中，复现 Claude Code 风格的多 Agent ultrareview 能力，而不依赖其官方付费托管审查服务。

这样更准确，也更稳，不容易写成误导性的宣传语。

## 推荐一句话介绍

> 在你自己的环境里，复刻 Claude Code 风格的多 Agent 并行代码审查工作流。

## 补充说明

这个 Skill 基于对 Claude Code 多 Agent 审查模式的公开逆向分析与实测复刻整理而来，然后适配成一个可复用的 OpenClaw Skill 包。

如果你想看更深入的背景和实现细节，可以继续读：
- `references/architecture.md`
- `references/diy-setup.md`

## License

发布仓库前请补上你希望采用的开源许可证。
