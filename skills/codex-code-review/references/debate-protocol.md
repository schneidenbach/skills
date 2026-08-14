# Adversarial Debate Protocol

## Running a debate round

For each candidate finding or concern, run up to 5 rounds.

### Your turn (Claude)

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
- Don't accept findings just because Codex stated them confidently
- Don't repeat the same argument if Codex already addressed it

### Codex's turn

Call `codex exec` with your challenge. Ask Codex to respond with one of:
- **DEFEND**: Maintain the original position with additional evidence
- **RETRACT**: Withdraw the finding or concern
- **REVISE**: Modify the finding or concern (provide the updated version)

### Convergence rules

- **Both agree it's real**: CONFIRMED. Stop.
- **Both agree it's not real**: DISMISSED. Stop.
- **Codex revises**: Continue debating the revised version. Do NOT reset the round count.
- **5 rounds with no convergence**: UNRESOLVED.

### Handling unexpected Codex responses

Codex won't always use the exact DEFEND/RETRACT/REVISE labels. When parsing:
- Look for semantic equivalents ("I stand by this" = DEFEND, "fair point, this isn't an issue" = RETRACT)
- If truly ambiguous, treat it as DEFEND and continue the debate
- If Codex repeats itself without adding new evidence, count it as a round but note the stalemate

## Reversed-role debate

Use when Codex found no issues (returned CONCEPT_LOOKS_SOUND or NO_ISSUES_FOUND).

Generate 2-3 concerns of your own to stress-test the concept or code. Present each to Codex and ask it to defend against your concern.

In reversed mode, the semantics flip:
- **Codex DEFENDS convincingly**: DISMISSED (your concern was unfounded)
- **Codex CONCEDES**: CONFIRMED (real issue found)
- **Codex says PARTIAL**: Continue debating the remaining gap
- **You withdraw**: DISMISSED
- **5 rounds, no convergence**: UNRESOLVED

If all stress-test concerns are dismissed: "Both reviewers agree: no significant issues found."

## Timeout and error handling

Set a 120-second timeout on each `codex exec` call. If Codex:
- **Times out**: Note the timeout, skip this finding, and continue with the next
- **Returns non-zero exit code**: Log the error, skip this finding
- **Returns empty or garbled output**: Treat as a timeout (skip and continue)

If more than half of Codex calls fail in the debate loop, stop and tell the user: `Codex is failing repeatedly mid-debate (<N>/<M> calls failed: <last error>). Retry, or finish with Claude-only judgment on the remaining items?` Wait for their decision. Do not silently downgrade.

## Cost awareness

Each debate round spawns a separate `codex exec` call. A deep review with 5 findings and 5 rounds per finding means up to 25 calls. For large reviews with many findings, Auto mode keeps costs reasonable by only debating the highest-severity items.
