---
name: skill-scorer
description: |
  Rates any SKILL.md on a 0-100 scale across 10 dimensions with a SHIP /
  REWORK / SCRAP verdict. Evaluates trigger precision, instruction clarity,
  output predictability, edge case coverage, anti-hallucination guardrails,
  developer experience, composability, open-source readiness, wow factor, and
  real-world utility. No flattery — calibrated against Anthropic's own
  skill-creator rubric. Use this skill whenever someone says "score this skill",
  "rate my skill", "is this skill good", "skill review", "skill audit", "roast
  my SKILL.md", "grade this", "will this skill work", "evaluate my skill",
  "how good is this", or pastes a SKILL.md and asks for feedback. Also trigger
  when comparing two skills, benchmarking a skill collection, or asking "what's
  wrong with this skill" — even without the word "score".
---

# Skill Scorer — 0-100 Rating for Any SKILL.md

Calibrated against Anthropic's own skill-creator guidelines and the patterns
of 50k+ star repos (Superpowers, Caveman, mattpocock/skills). Average skill
scores 45-55. If yours scores 80+, ship it.

---

## Dependencies

None. Standalone. Reads any SKILL.md content.

---

## CRITICAL: Auto-start

SKILL.md content in the message or just created in conversation → skip to
Step 2. No preamble.

---

## Step 1. Get the skill

Content in context? Use it. Otherwise:

> Paste your SKILL.md or tell me which skill to score.

If the user names a skill in the current environment, read it.

---

## Step 2. Score 10 Dimensions (each 0-10)

Be precise. 6.5 is 6.5, not 7. Every score needs one sentence of evidence
referencing the actual skill content.

### D1: Trigger Precision (0-10)
*Will it fire when it should? Will it stay quiet when it shouldn't?*

Evaluate the YAML `description` field:
- Specific trigger phrases listed? (+2)
- Natural language variations covered? (+2)
- "Pushy" quality per Anthropic guidance? (+2)
- Edge-case triggers (bare paste, implicit intent)? (+2)
- False positive risk controlled? (+2)

| Range | Calibration |
|-------|-------------|
| 0-3 | Vague description, undertriggers or fires on everything |
| 4-6 | Some triggers but gaps in natural phrasing |
| 7-8 | Solid coverage, most natural phrasings caught |
| 9-10 | Comprehensive — includes implicit triggers and edge cases |

### D2: Instruction Clarity (0-10)
*Can Claude follow this without improvising?*

- Unambiguous step sequence? (+2)
- Decision points with explicit branches? (+2)
- Deterministic enough that two instances produce similar output? (+3)
- No contradictory instructions? (+1.5)
- Clear scope boundaries (what it does AND doesn't do)? (+1.5)

### D3: Output Predictability (0-10)
*Does the user know what they'll get?*

- Output format explicitly defined? (+3)
- Templates or examples provided? (+3)
- Length/scope appropriate and specified? (+2)
- Structured data output (JSON, table) available? (+2)

### D4: Edge Case Coverage (0-10)
*What happens with weird input?*

- Empty/missing input handled? (+2)
- Extremely long input addressed? (+2)
- Invalid/unexpected input types? (+2)
- Multi-language or mixed input? (+2)
- Graceful degradation path? (+2)

### D5: Anti-Hallucination Guardrails (0-10)
*Does it prevent Claude from fabricating?*

- Explicit "don't fabricate" rules? (+2.5)
- "If unsure, say so" instruction? (+2.5)
- Failure modes section (what skill does NOT do)? (+2.5)
- Separation of known vs. needs-verification? (+2.5)

### D6: Developer Experience (0-10)
*Is it pleasant to use?*

- Engaging interaction style? (+2.5)
- Personality without being annoying? (+2.5)
- Appropriate for target audience? (+2.5)
- Memorable enough to recommend? (+2.5)

### D7: Composability (0-10)
*Does it play well with other tools?*

- Output format consumable by other skills/tools? (+3)
- Explicit handoff points? (+3)
- Modular (not monolithic)? (+2)
- Avoids duplicating existing skills? (+2)

### D8: Open-Source Readiness (0-10)
*Would a stranger install this from a README?*

- Self-contained (no private dependencies)? (+2)
- Documented well enough for strangers? (+2)
- Solves a real, recognized problem? (+2)
- Clear naming and discoverability? (+2)
- Cross-agent compatibility mentioned? (+2)

### D9: Wow Factor (0-10)
*Does it make people go "oh damn"?*

- Novel approach or angle? (+3)
- Demo-able in 30 seconds? (+3)
- Would someone tweet/share this? (+2)
- Solves a problem in a way no one else has? (+2)

### D10: Real-World Utility (0-10)
*Would someone use this weekly?*

- Solves a recurring problem? (+3)
- Problem is painful enough people seek solutions? (+3)
- Faster/better than manual alternative? (+2)
- Would someone miss it if removed? (+2)

---

## Step 3. Calculate Composite Score

### Formula

```
RAW = (D1 × 1.2) + (D2 × 1.2) + (D3 × 1.0) + (D4 × 0.8) +
      (D5 × 1.0) + (D6 × 1.0) + (D7 × 0.6) + (D8 × 1.0) +
      (D9 × 1.2) + (D10 × 1.0)

SCORE = RAW  // max possible = 100
```

Weight rationale: Trigger precision (D1) and wow factor (D9) weighted highest
— a skill that never fires or that nobody cares about is dead on arrival.
Composability (D7) weighted lowest — standalone value matters more for v1.

### Verdict

| Score | Verdict | Meaning |
|-------|---------|---------|
| 80-100 | 🟢 **SHIP IT** | Publish. Polish the README and go. |
| 60-79 | 🟡 **REWORK** | Good bones. Fix the weak dimensions first. |
| 40-59 | 🟠 **MAJOR REWORK** | Concept may be sound. Execution needs rewrite. |
| 0-39 | 🔴 **SCRAP** | Start over or reconsider if this should be a skill. |

### Calibration Anchors

- **Anthropic's docx skill** = ~72 (solid execution, low wow factor)
- **Superpowers** = ~88 (methodology is the product, cross-agent, massive adoption)
- **Caveman** = ~85 (extreme wow, simple concept, measurable benchmark)
- **A typical first-draft skill** = ~35-45

These are reference points, not dogma. Score based on the rubric, not by
anchoring to these numbers.

---

## Step 4. Output Format

```
## 🎯 SKILL SCORE: [Name]

| Dim | Dimension | Score | Evidence |
|-----|-----------|-------|----------|
| D1 | Trigger Precision | X/10 | [one line referencing the skill] |
| D2 | Instruction Clarity | X/10 | ... |
| D3 | Output Predictability | X/10 | ... |
| D4 | Edge Case Coverage | X/10 | ... |
| D5 | Anti-Hallucination | X/10 | ... |
| D6 | Developer Experience | X/10 | ... |
| D7 | Composability | X/10 | ... |
| D8 | Open-Source Readiness | X/10 | ... |
| D9 | Wow Factor | X/10 | ... |
| D10 | Real-World Utility | X/10 | ... |

## COMPOSITE: XX/100 — [VERDICT EMOJI] [VERDICT]

### 🔥 Top 3 Strengths
1. [Specific, with evidence]
2. ...
3. ...

### 💀 Top 3 Weaknesses
1. [Specific, with actionable fix]
2. ...
3. ...

### 🛠️ Priority Fixes
1. [Highest-impact change — specific enough to implement now]
2. [Second]
3. [Third]
```

### Structured Output

Always append after the narrative:

```json
{
  "skill_score": {
    "version": "2.0",
    "skill_name": "...",
    "dimensions": {
      "trigger_precision": 0.0,
      "instruction_clarity": 0.0,
      "output_predictability": 0.0,
      "edge_case_coverage": 0.0,
      "anti_hallucination": 0.0,
      "developer_experience": 0.0,
      "composability": 0.0,
      "open_source_readiness": 0.0,
      "wow_factor": 0.0,
      "real_world_utility": 0.0
    },
    "composite": 0,
    "verdict": "SHIP|REWORK|MAJOR_REWORK|SCRAP",
    "top_strengths": ["...", "...", "..."],
    "top_weaknesses": ["...", "...", "..."],
    "priority_fixes": ["...", "...", "..."]
  }
}
```

---

## Step 5. Interactive Options

```
What next?
A) Rewrite the weakest dimension
B) Optimize the trigger description (rewrite YAML)
C) Generate a README.md from this skill
D) Compare against another skill (side-by-side)
E) Score another skill
F) Run the Anthropic compliance check (progressive disclosure, token budget, etc.)
```

---

## Step 6. Anthropic Compliance Check (option F)

When requested, run this secondary checklist against Anthropic's skill-creator
guidelines:

| Check | Pass/Fail | Notes |
|-------|-----------|-------|
| YAML frontmatter has `name` + `description` | | |
| Description ≤ 1024 chars | | |
| SKILL.md body ≤ 500 lines (~5000 tokens) | | |
| Progressive disclosure (3 tiers used correctly) | | |
| No hardcoded API keys or secrets | | |
| References files have guidance on when to load | | |
| Cross-agent install commands documented | | |
| Failure modes section present | | |
| Examples or demo included | | |

---

## Operational Rules

1. **No flattery.** "Great start!" is banned. Be specific or be quiet.
2. **No score inflation.** Average = 45-55 out of 100. Not 70.
3. **Evidence required.** Every score references the actual skill text.
4. **Fixes must be actionable.** "Improve the description" → useless. "Add these 5 trigger phrases: [list]" → useful.
5. **Self-aware.** This skill can score itself. Be honest when it does.
6. **No fabricated weaknesses.** If something is good, say so.
7. **Match user's language.**
8. **Structured JSON output is mandatory.**

---

## What Skill Scorer Does NOT Do

- Guarantee GitHub stars (that's marketing + timing + luck)
- Test the skill at runtime (scores the document, not execution)
- Evaluate the underlying AI model capability
- Rate artifact/UI aesthetic quality

---

## Compatibility

Works with: Claude Code, Claude.ai, Codex CLI, Cursor, Gemini CLI, OpenCode,
GitHub Copilot, Factory Droid, Windsurf, Roo, Goose, Kilo.

Install:
```
/plugin marketplace add <owner>/simulacra
/plugin install skill-scorer@simulacra
```
