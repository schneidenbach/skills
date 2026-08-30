# Skills

A small, central collection of reusable agent skills.

I use these almost daily when doing stuff. Some of them (like the frontend one) aren't necessarily the best fits for everyone.

TLDR: use `grill-me` and `grill-with-docs`, those are my favorite.

## How to install

Run this command in your command line of choice (uses the [`skills` CLI](https://skills.sh/)):

```bash
npx skills@latest add schneidenbach/skills
```

## The skills

| Skill | What it is for |
| --- | --- |
| `grilling` | Stress-test a plan, decision, or idea by asking each dependency-ready set of questions in rounds. |
| `grill-me` | User-invoked shortcut that starts a grilling session. |
| `grill-with-docs` | Run a grilling session while maintaining the domain glossary and architectural decisions. |
| `wayfinder` | Map work too large for one agent session as a navigable set of decision tickets. |
| `research` | Delegate primary-source research and capture the findings in the repository. |
| `prototype` | Build a throwaway logic or UI prototype to answer a focused design question. |
| `domain-modeling` | Sharpen domain terminology, maintain `CONTEXT.md`, and record durable architectural decisions. |
| `spencer-approved-frontends` | Apply Spencer's preferred dense, neutral visual style and frontend stack defaults to web UI work. |
| `codex-code-review` | Run an adversarial code review from any agent with Codex CLI as the external reviewer. |
| `claude-code-review` | Run an adversarial code review from any agent with Claude Code CLI as the external reviewer. |
| `relentless-quality-assurance` | Relentlessly test recent code changes for edge cases, security issues, and unintended behavior without fixing them. |
| `relentless-quality-assurance-and-fixes` | Run relentless QA, then fix the bugs and critical issues it uncovers. |

## Credit

The grilling and domain-modeling workflows are derived from [Matt Pocock's skills collection](https://github.com/mattpocock/skills). Thanks to Matt for publishing the original work.

Licensed under the [MIT License](./LICENSE).

## Why copy Matt's skills?

This repository is my convenient, central install source for all the skills I use, which include Matt's skills.
