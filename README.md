# ultrareview

A reusable multi-agent code review skill inspired by Claude Code’s hidden `/ultrareview` workflow.

This repo packages the core idea into a portable skill: dispatch multiple specialized reviewers in parallel, cross-check their findings, then produce a consolidated bug report with severity levels.

## Why this exists

Claude Code’s built-in advanced review workflow is associated with paid / cloud-hosted review capabilities. This project recreates the **workflow pattern** as a self-hosted skill so you can get a very similar review style using your own agent runtime and your own model/API budget.

In practice, this means:
- you do **not** need access to Anthropic’s paid hosted ultrareview product
- you can run the review flow with your own local or self-managed agent environment
- your code can stay inside your own workspace / infrastructure
- your cost is whatever your own model calls cost, instead of a fixed hosted review fee

## What the skill does

`ultrareview` turns code review into a multi-agent system:

1. **Collect context**
   - review scope: files, folders, PR diff, or full repo
   - project rules from `CLAUDE.md` / `REVIEW.md`

2. **Spawn parallel review agents**
   Typical roles:
   - Logic Verifier
   - Security Sentinel
   - Performance Oracle
   - Boundary Inspector
   - Architecture Reviewer

3. **Cross-validate findings**
   - agents challenge each other’s conclusions
   - unconfirmed issues are discarded
   - duplicated findings are merged

4. **Generate a structured report**
   - 🔴 Critical
   - 🟡 Warning
   - 🟣 Pre-existing

## What you get

Compared with a single-pass code review prompt, this skill is designed to provide:
- better bug coverage through specialization
- lower false positives through cross-validation
- more actionable output through severity ranking and root-cause framing
- a reusable review workflow that can be adapted to local CLI, subagents, or CI

## Repository contents

```text
ultrareview/
├── README.md
├── README.zh-CN.md
├── SKILL.md
└── references/
    ├── architecture.md
    └── diy-setup.md
```

## Files

- `SKILL.md` — main skill definition, review process, output format, and implementation options
- `references/architecture.md` — architectural notes on the multi-agent review pattern, roles, cross-validation, and benchmarks
- `references/diy-setup.md` — practical setup guide for Agent Teams, custom subagents, plugin-based review, and CI/CD usage

## Usage

Install or copy the skill into your skill directory, then invoke it when you want:
- deep code review
- pre-merge inspection
- bug hunting
- security-oriented review
- multi-agent review of a large diff

Typical prompts:
- “Run ultrareview on `src/auth/`.”
- “Do a multi-agent review of this PR diff.”
- “Audit these changes for logic, security, performance, and edge cases.”

## Implementation paths

This repo documents four practical ways to use the workflow:

1. **Native Agent Teams**
   Best if your coding agent supports team-style parallel agents.

2. **Plugin-based setup**
   Good if you already use a compound / orchestration plugin.

3. **Custom subagents**
   Best for maximum control over agent roles and prompts.

4. **CI/CD integration**
   Useful for automated pull request review.

## Important framing

This project does **not** ship Anthropic’s proprietary hosted review backend.

What it provides is the **review architecture and skill packaging** needed to reproduce the same style of multi-agent review in your own environment.

So the practical value proposition is:
- **similar ultrareview-style capability**
- **without needing the paid hosted Claude Code review product itself**
- **using your own runtime and model budget**

If you want the shortest version:

> It gives you a self-hosted, ultrareview-style workflow for “free” in the product sense — meaning you avoid Anthropic’s paid hosted feature fee — but you still pay for whatever models / infrastructure you choose to run underneath.

## Recommended README tagline

> Recreate Claude Code–style multi-agent ultrareview in your own environment.

## Related notes

The skill was derived from public reverse-engineering and hands-on reconstruction of the multi-agent review pattern, then adapted into a reusable OpenClaw-compatible skill package.

For deeper background, see:
- `references/architecture.md`
- `references/diy-setup.md`

## License

Add the license you want before publishing the repository.
