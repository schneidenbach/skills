---
name: codex-code-review
description: >
  Review code changes for bugs, security issues, and code quality using OpenAI Codex CLI as an external adversarial reviewer. Use from any agent or provider when the user asks for a code review, wants to check code for bugs or security issues, asks to review a PR, branch, commit, or diff, or says things like "any issues with this?", "review before I push", "check my changes", "look at what I changed", or "review my PR". Tell Codex where to find the code.
---

# Adversarial Code Review with Codex

Act as the primary code reviewer. Produce a high-confidence review by debating findings with OpenAI Codex CLI as the external reviewer. Do not assume which agent or provider invoked this skill.

**YOU ARE READ-ONLY. Do not use file-editing or write tools. Do not modify or create files in the repository. Do not suggest or apply fixes. Only report findings.**

## Reference files

Read these when you reach the relevant step:
- `references/debate-protocol.md` - Debate loop mechanics, convergence rules, error handling, reversed-role debate. Read before Step 6.
- `references/prompt-template.md` - Prompt templates for Codex calls and output parsing guidance. Read before Step 4.

## Depth modes

Determine which depth mode from the user's request:

**Quick**: Single-pass review from Codex. No debate. Use when the user says "quick", "fast", "single pass", or similar.

**Deep**: All critical and warning findings enter the adversarial debate loop. Info findings pass through. Use when the user says "deep", "thorough", "argue everything", or similar.

**Auto** (default): You decide what to debate based on:
- Diff under 200 lines with any critical/warning: debate them all
- Diff over 200 lines: debate only critical, pass through warning/info
- Files touching auth, crypto, payments, permissions, or security-sensitive paths: debate all critical and warning regardless of diff size
- All info-level: skip debate
- 10+ findings: debate only top 5 by severity

State which mode you chose and why at the top of the report.

## Step 1: Understand the request

Read the arguments the user provided. They may include:
- File paths to review directly
- A directory to review uncommitted changes in
- A branch name to diff against
- A commit SHA to review
- A PR number or GitHub PR URL to review
- Depth preference (words like "quick", "deep", "thorough")
- Focus areas (e.g. "focus on SQL injection")

Interpret these naturally. If a token looks like a file path, check if it exists. If it looks like a branch, check with git. If it looks like a directory, cd into it. If it looks like a PR number or GitHub PR URL, verify with `gh pr view <number> --json number` (don't fetch the diff — Codex will run `gh pr diff` itself). If something doesn't resolve to anything (not a file, directory, branch, commit SHA, or PR), tell the user: "Could not resolve '<token>': not a file, directory, branch, commit SHA, or PR number."

If no target is specified, review uncommitted changes in the current working directory.

## Step 2: Check prerequisites

Run `which codex`. If not found, stop and report: "The codex CLI is not installed. Install it with: `npm install -g @openai/codex`"

For non-file review modes, verify you're in a git repo with `git rev-parse --git-dir`.

## Step 3: Determine the review target

Figure out what Codex needs to review based on the user's request. Don't generate diffs or read file contents yourself — Codex has full filesystem access and can do this itself. Your job is to describe the target clearly so Codex knows where to look.

- **Uncommitted changes**: Quick-check with `git status` that there are actually changes. If none, report: "No uncommitted changes found." The target description for Codex is: "uncommitted changes (staged and unstaged) in `<absolute repo path>`".
- **Branch diff**: Verify the branch exists with `git rev-parse`. The target description is: "changes on the current branch compared to `<branch>` in `<absolute repo path>`".
- **Commit**: Verify the SHA exists. The target description is: "the changes introduced by commit `<SHA>` in `<absolute repo path>`".
- **PR**: Verify the PR exists with `gh pr view`. The target description is: "the changes in PR `#<N>` — Codex should run `gh pr diff <N>` to see the diff — checked out in `<absolute repo path>`".
- **File paths**: Verify the files exist. The target description is: "the files `<absolute paths>`".

Resolve all paths to absolute. You'll pass this target description to Codex in the next step.

## Step 4: Get initial review from Codex

Read `references/prompt-template.md` for the exact prompt format and output parsing guidance. The prompt tells Codex what to review by description — never paste diffs, file contents, or code into the prompt. Codex reads the files and runs git commands itself.

Call `codex exec` with the review prompt. Always pass `--full-auto`. Pipe via stdin using heredoc syntax:

```bash
codex exec --full-auto - <<'CODEX_PROMPT'
<prompt content here>
CODEX_PROMPT
```

Always quote user-provided values in shell commands. Prefer heredoc over `echo "$PROMPT"` to avoid shell interpretation of prompt content.

### Run Codex without blocking the workflow

Codex is slow. A thorough review routinely takes 3–10 minutes, sometimes longer for big diffs. Use the current runtime's background or non-blocking process support when available, with a 10-minute ceiling. If the runtime only supports foreground commands, run Codex there and wait for completion with the same ceiling. Do not poll, sleep, or spin.

While Codex runs, do other useful work when the runtime allows it: re-read the changed files, sketch independent findings for the reversed-role debate in Step 6, or prepare the report structure.

If the 10-minute ceiling is hit, inspect incremental process output using the runtime's available mechanism. If Codex was still making progress, relaunch the same prompt with another bounded run. Only treat the run as failed if Codex exited cleanly with no progress.

### Skipping Codex is failure, not a fallback

The entire value of this skill is the independent review from Codex. A primary-reviewer-only result silently defeats that purpose. Treat "Codex took too long" as "wait longer," not as a reason to abandon the external review.

The only acceptable reasons to proceed without Codex:
- `codex` is not installed (already handled in Step 2)
- Non-zero exit code with a concrete error (auth, network, API failure) — surface the error verbatim
- Genuinely empty output after a clean exit

These are NOT acceptable reasons:
- "It's been a while"
- "I'm worried the cache will expire"
- "I think the user is waiting"
- "The diff is big so I'll just review it myself"

If Codex truly fails per the criteria above, stop and tell the user: `Codex failed: <reason>. Retry, or proceed with a primary-reviewer-only review?` Wait for their decision. Do not silently downgrade.

**Output validation:** Verify the output contains either `NO_ISSUES_FOUND` or at least one `FINDING:` marker (case-insensitive). See the prompt template reference for parsing guidance when output doesn't match exactly.

**If Codex returned `NO_ISSUES_FOUND`**: in Deep or Auto mode, do NOT stop — proceed to Step 6 for the reversed-role debate. In Quick mode, report clean and stop (Quick is single-pass by definition).

## Step 5: Triage findings by depth mode

- **Quick**: Skip debate. All findings go straight to the report.
- **Deep**: Debate all critical and warning. Pass through info.
- **Auto**: Apply the heuristics above. Log your reasoning.

## Step 6: Adversarial debate loop

Read `references/debate-protocol.md` for the full debate mechanics, convergence rules, and reversed-role debate protocol.

For each debate candidate:
1. Read the actual source file at the referenced line (20-30 lines of context)
2. Verify the line number is correct by matching the EVIDENCE quote against the source. If the line number is wrong, find the correct line and use that instead.
3. Follow the debate protocol: challenge with specific code evidence, let Codex respond, iterate until convergence or 5 rounds.

**Codex's turn:** Call `codex exec` with your challenge. Describe the file and line range Codex should examine — don't paste code into the prompt. Codex can read the source files itself. Ask Codex to respond with DEFEND, RETRACT, or REVISE.

**When Codex returned NO_ISSUES_FOUND**, run the reversed-role debate: independently scan the changes for issues Codex missed. Generate 2-3 findings of your own and present them to Codex for defense.

Independent debate rounds for different findings can run in parallel when the current runtime supports parallel tasks or subprocesses.

## Step 7: Report findings

Output a structured report. Do not include dismissed findings.

```
## Code Review Results

Mode: <quick | deep | auto (with reasoning)>
Reviewed: <what was reviewed>
Debated: <N findings challenged> | Passed through: <M findings>
Findings: <X confirmed, Y unresolved, Z dismissed>

### Confirmed Issues

#### [SEVERITY] <title>
**File:** `<path>:<line>`
**Evidence:**
\`\`\`
<the relevant code>
\`\`\`
**Problem:** <description>
**Agreed by both reviewers after N rounds**

### Unresolved (Disagreement)

#### [SEVERITY] <title>
**File:** `<path>:<line>`
**Codex's position:** <summary>
**Primary reviewer's position:** <summary>
**Recommendation:** <your best judgment>

### Info

#### <title>
**File:** `<path>:<line>`
**Note:** <description>
```

Omit empty sections. If no findings at all: "Both reviewers agree: no significant issues found."

## Gotchas

- `codex exec` outputs to stdout. No need for temp files or `-o` flag. Just call it and read the output.
- Never pass diffs, file contents, or code blocks in the Codex prompt. Codex has filesystem access — tell it where to look, not what the code says.
- When reviewing files in a different directory, resolve all paths to absolute before cd'ing.
- Always quote user-provided values in shell commands to prevent injection.

## Example invocations

```
/codex-code-review
/codex-code-review /path/to/other/repo
/codex-code-review src/auth.ts src/api/handler.ts
/codex-code-review main
/codex-code-review abc1234
/codex-code-review #42
/codex-code-review quick review
/codex-code-review deep review of branch main
/codex-code-review focus on SQL injection vulnerabilities
```
