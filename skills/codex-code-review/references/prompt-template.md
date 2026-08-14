# Codex Prompt Template: Code Review

## Initial review prompt

Use this template for the Step 4 call to Codex. Replace placeholders in angle brackets. Never paste diffs, file contents, or code into the prompt — point Codex at the target by description and let it read the files itself.

```
Review the following code changes and identify potential issues.

REPO: <absolute path to repo>
TARGET: <target description from Step 3, e.g., "uncommitted changes (staged and unstaged)" or "the changes introduced by commit abc1234" or "the files /abs/path/a.ts /abs/path/b.ts">

Use git and the filesystem to read the relevant code yourself. For diffs, run `git diff HEAD`, `git show <SHA>`, `git diff <merge-base>..HEAD`, or similar as appropriate. For file targets, read the files directly.

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

Use this when challenging a Codex finding in the debate loop. Don't paste source code into the prompt — Codex can read the file itself.

```
You previously flagged this issue in a code review:

FINDING: <title>
FILE: <absolute file path>
LINE: <line>
SEVERITY: <severity>
EVIDENCE: <code quote>
REASONING: <original reasoning>

Re-read <file> around line <line> (20-30 lines of context). Here is my challenge:

<Claude's challenge with specific evidence — describe what you saw in the file, don't paste the code>

Based on this challenge, respond with exactly one of:
- DEFEND: <maintain your position with additional evidence>
- RETRACT: <withdraw the finding and explain why>
- REVISE: <modify the finding, providing the updated version in full>
```

## Reversed-role challenge prompt

Use when Codex found no issues and Claude is stress-testing. Point Codex back at the target — don't paste the diff.

```
You reviewed the changes in <repo path> (<target description>) and found no issues (NO_ISSUES_FOUND).

Re-examine the changes. I believe there is a potential issue:

FINDING: <Claude's finding title>
FILE: <absolute file path>
LINE: <line>
SEVERITY: <severity>
EVIDENCE: <code quote>
REASONING: <Claude's reasoning>

Defend the code against this finding, or concede if it has merit.
Respond with one of:
- DEFEND: <why the code is correct>
- CONCEDE: <why this is a real issue>
- PARTIAL: <the finding has some merit but is overstated; here is what remains>
```

## Parsing Codex output

Codex may not follow the format exactly. When parsing:
- Match field names case-insensitively (FINDING, Finding, finding)
- Strip any preamble text before the first FINDING: or NO_ISSUES_FOUND marker
- If a field is missing, infer it from context rather than discarding the whole finding
- If LINE is wrong (doesn't match the EVIDENCE quote), search the source file for the quoted code and correct the line number
- If the output is completely unstructured, extract findings manually and note in the report that formatting was non-standard
