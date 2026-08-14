# Claude Prompt Template: Code Review

## Initial review prompt

Use this template for the Step 4 call to Claude. Replace placeholders in angle brackets. Never paste diffs, file contents, or code into the prompt — point Claude at the target by description and let it read the files itself.

```
You are an adversarial code reviewer. Review the following code changes and identify potential issues. Your reviewer counterpart (Codex) will challenge your findings, so cite specific evidence.

REPO: <absolute path to repo>
TARGET: <target description from Step 3, e.g., "uncommitted tracked changes (staged and unstaged) and any untracked files" or "the changes introduced by commit abc1234" or "the files /abs/path/a.ts /abs/path/b.ts">

Use git, the filesystem, and shell access to read the relevant code yourself. For tracked diffs, run `git diff HEAD`, `git show <SHA>`, `git diff <merge-base>..HEAD`, or similar as appropriate. For untracked files, list them with `git ls-files --others --exclude-standard` and then read each one directly. For file targets, read the files directly. Do not ask the user to paste anything.

FOCUS AREAS: <user-specified focus areas, or "none specified" if absent>

Look for:
- Bugs: logic errors, off-by-one, null/undefined access, type mismatches
- Security: injection, auth bypass, data exposure, unsafe deserialization
- Race conditions: shared state, missing locks, async ordering issues
- Error handling: swallowed errors, missing cleanup, unclosed resources
- Performance: N+1 queries, unbounded loops, missing pagination, memory leaks

For each finding, use this exact format:

FINDING: <short title>
FILE: <absolute file path>
LINE: <source file line number, NOT the diff offset>
SEVERITY: critical | warning | info
EVIDENCE: <quote the specific code>
REASONING: <why this is a problem>

Important: LINE must be the actual source file line number where the issue occurs, not the diff position or hunk offset.

If you find no issues, output:
NO_ISSUES_FOUND: <brief explanation>

Do not add preamble or commentary outside of this format.
```

## Debate challenge prompt

Use this when challenging a Claude finding in the debate loop. Don't paste source code into the prompt — Claude can read the file itself.

```
You previously flagged this issue in a code review:

FINDING: <title>
FILE: <absolute file path>
LINE: <line>
SEVERITY: <severity>
EVIDENCE: <code quote>
REASONING: <original reasoning>

Re-read <file> around line <line> (20-30 lines of context). Here is my challenge:

<Codex's challenge with specific evidence — describe what you saw in the file, don't paste the code>

Based on this challenge, respond with exactly one of:
- DEFEND: <maintain your position with additional evidence>
- RETRACT: <withdraw the finding and explain why>
- REVISE: <modify the finding, providing the updated version in full>
```

## Reversed-role challenge prompt

Use when Claude found no issues and Codex is stress-testing. Point Claude back at the target — don't paste the diff.

```
You reviewed the changes in <repo path> (<target description>) and found no issues (NO_ISSUES_FOUND).

Re-examine the changes. I believe there is a potential issue:

FINDING: <Codex's finding title>
FILE: <absolute file path>
LINE: <line>
SEVERITY: <severity>
EVIDENCE: <code quote>
REASONING: <Codex's reasoning>

Defend the code against this finding, or concede if it has merit.
Respond with one of:
- DEFEND: <why the code is correct>
- CONCEDE: <why this is a real issue>
- PARTIAL: <the finding has some merit but is overstated; here is what remains>
```

## Parsing Claude output

Claude may not follow the format exactly. When parsing:
- Match field names case-insensitively (FINDING, Finding, finding)
- Strip any preamble text before the first FINDING: or NO_ISSUES_FOUND marker
- If a field is missing, infer it from context rather than discarding the whole finding
- If LINE is wrong (doesn't match the EVIDENCE quote), search the source file for the quoted code and correct the line number
- If the output is completely unstructured, extract findings manually and note in the report that formatting was non-standard
