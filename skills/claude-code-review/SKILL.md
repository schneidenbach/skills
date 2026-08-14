---
name: claude-code-review
description: Use Claude Code CLI as an external reviewer from any agent. Trigger for code reviews of files, changes, branches, commits, or pull requests when an independent Claude pass would help find bugs, security issues, or regressions.
---
Review the user's target without modifying it. If no target is given, review staged, unstaged, and untracked changes in the current repository.
Verify `claude` is available, then send one concise review prompt through `claude -p --permission-mode plan`. Give Claude the absolute repository path, target, and requested focus; do not paste the diff. Tell Claude to inspect the repository itself and return only actionable findings with severity, file, line, evidence, and reasoning.
Check every finding against the source. Challenge questionable findings once through the same read-only command; keep confirmed findings, label unresolved disagreements, and drop false positives.
If Claude is unavailable or fails, report the error and ask whether to continue with a primary-reviewer-only pass. Otherwise report confirmed issues from highest to lowest severity. If none remain, say no significant issues were found.
