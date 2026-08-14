# Skills

A small, central collection of reusable agent skills.

| Skill | What it is for |
| --- | --- |
| `grilling` | Stress-test a plan or design through a focused, one-question-at-a-time interview. |
| `grill-me` | User-invoked shortcut that starts a full grilling session. |
| `grill-me-short` | User-invoked shortcut that starts a condensed grilling session. |
| `grilling-short` | Condensed grilling workflow used by `grill-me-short`. |
| `domain-modeling` | Sharpen domain terminology, maintain `CONTEXT.md`, and record durable architectural decisions. |
| `spencer-approved-frontends` | Apply Spencer's preferred dense, neutral visual style and frontend stack defaults to web UI work. |
| `codex-code-review` | Have Claude Code drive an adversarial code review with Codex as the second reviewer. |
| `claude-code-review` | Have Codex drive an adversarial code review with Claude Code as the second reviewer. |

## Install

Install interactively from the repository with the [`skills` CLI](https://skills.sh/):

```sh
npx skills@latest add <owner>/<repo>
```

From a local checkout, run `npx skills@latest add .` in the repository root.

Or install one skill directly:

```sh
npx skills@latest add <owner>/<repo> --skill grilling
```

## Credit

The grilling and domain-modeling workflows are derived from [Matt Pocock's skills collection](https://github.com/mattpocock/skills). Thanks to Matt for publishing the original work.

Licensed under the [MIT License](./LICENSE).
