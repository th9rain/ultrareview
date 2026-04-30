# ultrareview

Recreate Claude Code–style multi-agent code review in your own environment.

`ultrareview` is a reusable skill that packages a high-signal review workflow: dispatch multiple specialized review agents in parallel, let them challenge each other’s findings, then merge the confirmed issues into a single structured report.

## Why this repo exists

Claude Code’s more advanced review workflow is associated with hosted / paid review capabilities. This repo does **not** ship Anthropic’s private review backend.

Instead, it reconstructs the **workflow pattern** behind ultrareview so you can run a similar multi-agent review flow with your own agent runtime, your own model provider, and your own infrastructure.

That means:
- no dependency on Anthropic’s hosted ultrareview product
- no requirement to upload code to a third-party review service you do not control
- no fixed per-review hosted fee from the original product
- your actual cost is simply whatever your own model / infrastructure usage costs

If you want the shortest description:

> A self-hosted ultrareview-style workflow, without needing the paid hosted Claude Code review product.

## What it does

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

## Why this workflow is better than a single review prompt

A single-pass review prompt is easy, but it often mixes:
- shallow coverage
- inconsistent severity judgment
- duplicated findings
- more false positives

`ultrareview` improves this by combining:
- **specialization** — each agent looks from a different angle
- **parallelism** — the same change is reviewed simultaneously from multiple perspectives
- **cross-examination** — agents challenge each other before a finding survives
- **aggregation** — the final output is deduplicated and severity-ranked

The result is a review flow that is generally more useful for:
- bug hunting
- security-oriented inspection
- large diffs
- high-risk refactors
- pre-merge review for important changes

## Origin

This skill was created from reverse-engineering and reconstructing the multi-agent review pattern exposed by Claude Code source artifacts, then adapting that pattern into a reusable OpenClaw-compatible skill package.

The key insight is simple:

> The real value is not just “ask one strong model to review code.”
> The value is the **system design**: multiple reviewers, specialized roles, falsification, and report synthesis.

## What “free ultrareview” really means

People often summarize this as “use Claude Code ultrareview for free.”

That wording is directionally understandable, but technically imprecise.

What this repo actually gives you is:
- a **self-hosted reproduction of the ultrareview-style workflow**
- **without paying for Anthropic’s hosted review product itself**
- while still using **your own underlying model/API budget**

So the accurate claim is:

> You can get ultrareview-style multi-agent review capability without buying the official hosted product.

Not:
- “this contains Anthropic’s private backend”
- “this makes model usage free”
- “this is literally the official feature unlocked”

## Repository structure

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

## Example use cases

Typical prompts:
- “Run ultrareview on `src/auth/`.”
- “Review this PR diff with logic, security, performance, and edge-case agents.”
- “Do a deep pre-merge audit for this refactor.”
- “Run a multi-agent security and correctness review on these changed files.”

Typical use scenarios:
- deep code review
- pre-merge inspection
- bug hunting
- security review
- large PR triage
- high-risk production changes

## What the packaged skill focuses on

This repo focuses on the reusable parts:
- the review process
- the agent-role decomposition
- the cross-validation pattern
- the report structure
- multiple deployment paths

It is intentionally lightweight and portable, so you can adapt it to:
- local CLI workflows
- OpenClaw / skill-based systems
- self-managed coding-agent environments
- GitHub Actions or other CI pipelines

## Related notes

For the deeper technical background, see:
- `references/architecture.md`
- `references/diy-setup.md`

## License

Choose and replace the placeholder `LICENSE` file before publishing.
