# Adversarial Debate Protocol

## Running a debate round

For each candidate finding or concern, run up to 5 rounds.

### Your turn (Codex)

1. Read relevant source material (code, docs, design files) with 20-30 lines of context around the referenced location
2. Form your own independent assessment before responding
3. Build a challenge or agreement using specific evidence

**What makes a strong challenge:**
- Challenge the evidence, not just the conclusion. Quote the specific code or design element.
- Cite concrete constraints: performance requirements, API contracts, backwards compatibility guarantees.
- Propose a specific scenario where the concern does or does not apply.
- If you agree, add new evidence that strengthens the finding rather than just saying "I agree."

**Avoid weak challenges:**
- Don't challenge based on general principles without grounding in the specific code or design
- Don't accept findings just because Claude stated them confidently
- Don't repeat the same argument if Claude already addressed it

### Claude's turn

Call `claude -p` with your challenge. Ask Claude to respond with one of:
- **DEFEND**: Maintain the original position with additional evidence
- **RETRACT**: Withdraw the finding or concern
- **REVISE**: Modify the finding or concern (provide the updated version)

### Convergence rules

- **Both agree it's real**: CONFIRMED. Stop.
- **Both agree it's not real**: DISMISSED. Stop.
- **Claude revises**: Continue debating the revised version. Do NOT reset the round count.
- **5 rounds with no convergence**: UNRESOLVED.

### Handling unexpected Claude responses

Claude won't always use the exact DEFEND/RETRACT/REVISE labels. When parsing:
- Look for semantic equivalents ("I stand by this" = DEFEND, "fair point, this isn't an issue" = RETRACT)
- If truly ambiguous, treat it as DEFEND and continue the debate
- If Claude repeats itself without adding new evidence, count it as a round but note the stalemate

## Reversed-role debate

Use when Claude found no issues (returned CONCEPT_LOOKS_SOUND or NO_ISSUES_FOUND).

Generate 2-3 concerns of your own to stress-test the concept or code. Present each to Claude and ask it to defend against your concern.

In reversed mode, the semantics flip:
- **Claude DEFENDS convincingly**: DISMISSED (your concern was unfounded)
- **Claude CONCEDES**: CONFIRMED (real issue found)
- **Claude says PARTIAL**: Continue debating the remaining gap
- **You withdraw**: DISMISSED
- **5 rounds, no convergence**: UNRESOLVED

If all stress-test concerns are dismissed: "Both reviewers agree: no significant issues found."

## Timeout and error handling

Set a 180-second timeout on each `claude -p` call. If Claude:
- **Times out**: Note the timeout, skip this finding, and continue with the next
- **Returns non-zero exit code**: Log the error, skip this finding
- **Returns empty or garbled output**: Treat as a timeout (skip and continue)

If more than half of Claude calls fail in the debate loop, stop the loop and surface the failure pattern to the user: `Claude is failing repeatedly mid-debate (<N>/<M> calls failed: <last error>). Retry, or finish with Codex-only judgment on the remaining items?` Wait for their decision. Do not silently downgrade — the user invoked this skill to get a second opinion, and quietly switching to a single-reviewer report defeats that.

## Cost awareness

Each debate round spawns a separate `claude -p` call. A deep review with 5 findings and 5 rounds per finding means up to 25 calls. For large reviews with many findings, Auto mode keeps costs reasonable by only debating the highest-severity items.
