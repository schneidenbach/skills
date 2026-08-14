# Skills

A small, central collection of reusable agent skills.

I use these almost daily when doing stuff. Some of them (like the frontend one) aren't necessarily the best fits for everyone.

TLDR: use `grill-me-short` and `grill-with-docs-short`, those are my favorite.

## How to install

Run this command in your command line of choice (uses the [`skills` CLI](https://skills.sh/)):

```bash
npx skills@latest add schneidenbach/skills
```

| Skill | What it is for |
| --- | --- |
| `grilling` | Stress-test a plan or design through a focused, one-question-at-a-time interview. |
| `grill-me` | User-invoked shortcut that starts a full grilling session. |
| `grill-me-short` | User-invoked shortcut that starts a condensed grilling session. |
| `grill-with-docs` | Run a full grilling session while maintaining the domain glossary and architectural decisions. |
| `grill-with-docs-short` | Run a condensed grilling session while maintaining the domain glossary and architectural decisions. |
| `grilling-short` | Condensed grilling workflow used by `grill-me-short`. |
| `domain-modeling` | Sharpen domain terminology, maintain `CONTEXT.md`, and record durable architectural decisions. |
| `spencer-approved-frontends` | Apply Spencer's preferred dense, neutral visual style and frontend stack defaults to web UI work. |
| `codex-code-review` | Run an adversarial code review from any agent with Codex CLI as the external reviewer. |
| `claude-code-review` | Run an adversarial code review from any agent with Claude Code CLI as the external reviewer. |

## Credit

The grilling and domain-modeling workflows are derived from [Matt Pocock's skills collection](https://github.com/mattpocock/skills). Thanks to Matt for publishing the original work.

Licensed under the [MIT License](./LICENSE).
