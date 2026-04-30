# ultrareview

> Recreate Claude Code–style multi-agent code review in your own environment.

`ultrareview` is a reusable skill that packages a high-signal review workflow: dispatch multiple specialized review agents in parallel, let them challenge each other’s findings, then merge the confirmed issues into a single structured report.

## Why this matters

Most AI code review today is still basically:
- one model
- one pass
- one mixed bag of comments

That works, but it often produces:
- shallow coverage
- repeated findings
- weak severity judgment
- more false positives than you want

`ultrareview` applies a different pattern:
- **specialized reviewers** instead of one generic reviewer
- **parallel review** instead of serial thinking
- **cross-validation** instead of blindly trusting first impressions
- **structured aggregation** instead of dumping raw notes

The result is a review flow that is much better suited to:
- pre-merge review for important changes
- bug hunting
- security-oriented review
- large diffs
- high-risk refactors
- codebases where correctness matters more than style commentary

## What this repo is

This repo does **not** contain Anthropic’s private hosted review backend.

It reconstructs the **workflow pattern** behind ultrareview so you can run a similar multi-agent review flow with:
- your own agent runtime
- your own model provider
- your own infra / local environment

So the accurate value proposition is:

> A self-hosted ultrareview-style workflow, without needing the paid hosted Claude Code review product.

What that means in practice:
- you do **not** need Anthropic’s official hosted ultrareview service
- you do **not** need to send code to a review system you do not control
- you **do** still pay for your own model/API/infrastructure usage

So this is not “magic free inference.”
It is “bring your own runtime, reproduce the workflow, avoid the hosted product fee.”

## Core workflow

`ultrareview` turns code review into a multi-agent system:

1. **Collect context**
   - files, folders, PR diff, or full repo
   - project-specific rules from `CLAUDE.md` and `REVIEW.md`

2. **Spawn parallel specialists**
   Typical roles include:
   - Logic Verifier
   - Security Sentinel
   - Performance Oracle
   - Boundary Inspector
   - Architecture Reviewer

3. **Cross-validate findings**
   - agents try to disprove each other’s claims
   - unconfirmed issues are dropped
   - overlapping findings are deduplicated

4. **Produce a structured report**
   - 🔴 Critical
   - 🟡 Warning
   - 🟣 Pre-existing

## Quick start

### Option 1: Use the skill package directly

Copy this repo into your skill directory or reuse the files in your own skill system:

```text
ultrareview/
├── SKILL.md
└── references/
    ├── architecture.md
    └── diy-setup.md
```

Then invoke it for tasks like:
- deep code review
- security review
- large PR review
- correctness-focused review

Example prompts:

```text
Run ultrareview on src/auth/
Review this PR diff with logic, security, performance, and edge-case agents
Do a deep pre-merge audit for this refactor
```

### Option 2: Adapt the prompts to your own coding agent

If your environment supports:
- subagents
- team/parallel agent execution
- slash-command prompt files
- CI headless CLI review

then you can directly adapt the role prompts and orchestration guidance from:
- `SKILL.md`
- `references/diy-setup.md`

## Example output

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

## Repository structure

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

## Files

- `SKILL.md` — main skill definition, review process, report format, and implementation options
- `references/architecture.md` — architecture notes on multi-agent review, agent roles, cross-validation, and benchmark framing
- `references/diy-setup.md` — practical setup guide for Agent Teams, custom subagents, plugin-based review, and CI/CD usage

## Implementation paths

This repo documents four practical ways to apply the workflow:

1. **Native Agent Teams**  
   Best when your coding agent supports team-style parallel agents.

2. **Plugin-based setup**  
   Useful if you already use a compound orchestration plugin.

3. **Custom subagents**  
   Best when you want full control over roles, prompts, and consolidation logic.

4. **CI/CD integration**  
   Useful for automated pull-request review pipelines.

## What “free ultrareview” really means

People often summarize this as “use Claude Code ultrareview for free.”

That wording is understandable, but sloppy.

What this repo actually gives you is:
- a **self-hosted reproduction of the ultrareview-style workflow**
- **without paying for Anthropic’s hosted review product itself**
- while still using **your own model/API budget**

So the accurate claim is:

> You can get ultrareview-style multi-agent review capability without buying the official hosted product.

Not:
- “this contains Anthropic’s private backend”
- “this makes model usage free”
- “this literally unlocks the official hidden feature”

## Recommended GitHub metadata

**Repository description**

> Recreate Claude Code–style multi-agent ultrareview with a self-hosted skill workflow.

**Suggested topics**

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

## Origin

This skill was built from reverse-engineering and reconstructing the multi-agent review pattern exposed by Claude Code source artifacts, then adapting that pattern into a reusable OpenClaw-compatible skill package.

The key insight is simple:

> The real leverage is not just “ask one strong model to review code.”
> The leverage is the **system design**: multiple reviewers, specialized roles, falsification, and synthesis.

## Related reading

- `references/architecture.md`
- `references/diy-setup.md`
- `README.zh-CN.md`

## License

MIT
