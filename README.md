<p align="center">
  <img src="assets/simulacra_dark_icon.png" width="80" />
</p>

<h1 align="center">Simulacra</h1>

<p align="center">
  <strong>Your code reviewed by ghost personas before it ships.</strong>
</p>

<p align="center">
  <a href="#before--after">Before/After</a> •
  <a href="#install">Install</a> •
  <a href="#the-ghosts">The Ghosts</a> •
  <a href="#skill-scorer">Skill Scorer</a> •
  <a href="#how-it-works">How It Works</a> •
  <a href="#benchmarks">Benchmarks</a>
</p>

<p align="center">
  <img src="https://img.shields.io/github/stars/mturac/simulacra?style=flat&color=yellow" alt="Stars" />
  <img src="https://img.shields.io/badge/license-MIT-blue" alt="License" />
  <img src="https://img.shields.io/badge/agents-12+-green" alt="Agents" />
</p>

---

## Before / After

**Without Simulacra** — you ship this:

```typescript
function applyDiscount(cart) {
  if (cart.total >= 500) {
    cart.items.forEach(item => item.price *= 0.8);
  }
  return cart;
}
```

> "Looks fine. Ship it." ✅

**With Simulacra** — three ghosts find three bugs:

> **👤 Reckless Rick** (The Adversary):
> "Your `forEach` mutates prices in place. I hit Back twice — 630 TL cart
> drops to 322 TL. Your company eats the 308 TL difference."
>
> 🔥 **IDEMPOTENCY** — 🔴 Critical
>
> **👤 Corporate Claire** (Enterprise Edge):
> "I'm paying in EUR. `cart.total = 200` fails the `>= 500` check.
> My 7000 TRY order gets no discount. I'm switching vendors."
>
> 🔥 **BUSINESS-LOGIC** — 🟠 High
>
> **👤 OOM Reaper** (Chaos Monkey):
> "Memory spike at item 7 of 14. Half the cart is discounted, half isn't.
> No rollback. The corruption persists to your database."
>
> 🔥 **PARTIAL-FAILURE** — 🔴 Critical

Three bugs. Zero lines of test code. Before the PR even opens.

---

## Install

### Claude Code (official plugin)

```
/plugin marketplace add mturac/simulacra
/plugin install simulacra@simulacra
/plugin install skill-scorer@simulacra
```

### Codex CLI

```bash
cp -r skills/simulacra ~/.codex/skills/
cp -r skills/skill-scorer ~/.codex/skills/
```

### Cursor / Windsurf / Roo

Copy `skills/simulacra/SKILL.md` to your project's `.cursor/skills/` (or equivalent) directory.

### Gemini CLI / OpenCode / GitHub Copilot CLI

```bash
cp -r skills/simulacra ~/.config/gemini/skills/   # gemini
cp -r skills/simulacra ~/.opencode/skills/         # opencode
```

### Vercel skills.sh

```bash
npx skills add mturac/simulacra
```

### Manual (any agent)

Copy the `skills/` folder to your agent's skill directory. The skills are pure
markdown — no dependencies, no build step, no runtime.

---

## The Ghosts

Every code review spawns **3 ghost personas** by default (up to 7 on request):

| Ghost | Perspective | Hunts For |
|-------|-------------|-----------|
| 👤 **The Adversary** | User who breaks things | Race conditions, double-submit, replay attacks, back-button abuse |
| 🏢 **Enterprise Edge** | Complex business customer | Multi-currency, tax exemptions, B2B rules, locale, permissions |
| 🐵 **Chaos Monkey** | Infrastructure bad day | Timeouts, partial failures, OOM, connection drops, network partitions |
| 🧑‍💻 **Junior Dev** *(+request)* | Future maintainer | Readability, magic numbers, implicit knowledge, missing docs |
| 📈 **The Scaler** *(+request)* | 1000x traffic | N+1 queries, unbounded loops, missing pagination, memory growth |
| 🔒 **The Auditor** *(+request)* | Security & compliance | Injection, auth bypass, data exposure, sensitive logging |
| ⏰ **Time Traveler** *(+request)* | 6 months from now | Dependency rot, API deprecation, schema drift, tech debt |

Each ghost produces:
- **Chaos Timeline** — step-by-step simulation with realistic timestamps
- **Structured Testimony** — what they did, what broke, evidence from your code
- **Bug Report** — category, severity, affected line, fix suggestion
- **JSON Output** — machine-readable for CI pipelines and issue trackers

---

## Skill Scorer

**Bonus skill included.** Rate any SKILL.md on a 0-100 scale.

```
> Score this skill: [paste any SKILL.md]
```

10 dimensions. Weighted composite. SHIP / REWORK / SCRAP verdict:

| Score | Verdict |
|-------|---------|
| 80-100 | 🟢 **SHIP IT** |
| 60-79 | 🟡 **REWORK** |
| 40-59 | 🟠 **MAJOR REWORK** |
| 0-39 | 🔴 **SCRAP** |

Calibrated against Anthropic's own skill-creator rubric. Average skill scores 45-55.

---

## How It Works

```
┌──────────────┐
│  Your Code   │
└──────┬───────┘
       │
       ▼
┌──────────────┐     ┌─────────────────────────┐
│ Complexity   │────▶│ Trivial? → One-line      │
│ Gate         │     │ "Low risk" + stop        │
└──────┬───────┘     └─────────────────────────┘
       │ Non-trivial
       ▼
┌──────────────┐
│ Butterfly    │  6 checks: idempotency, mutation,
│ Effect Scan  │  transactions, races, cache, biz logic
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Ghost        │  3-7 personas spawned
│ Personas     │  Each with unique failure perspective
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Structured   │  Timeline + Testimony + Bug Report
│ Simulation   │  per ghost (mandatory slots)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Resolution   │  Tier 1: Quick Fix
│ Tiers        │  Tier 2: Robust Architecture
│              │  Tier 3: Generated Test Suite
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ JSON Output  │  Machine-readable bug report
│ + Summary    │  for CI/CD and issue trackers
└──────────────┘
```

---

## Benchmarks

Simulacra v2 tested against 25 common code snippets with known bugs:

| Metric | Result |
|--------|--------|
| True positives (real bugs found) | 68/82 (83%) |
| False positives (fabricated bugs) | 3/82 (3.7%) |
| Unique bugs (not caught by linters) | 41/68 (60%) |
| Average ghosts needed to find all bugs | 2.4 |
| Bugs found by Adversary ghost only | 23 |
| Bugs found by Enterprise ghost only | 12 |
| Bugs found by Chaos ghost only | 15 |

*Methodology: 25 TypeScript/Python snippets with seeded bugs (idempotency, race, partial-failure, business-logic). Each run 3x. Scored against known bug list.*

---

## Philosophy

**Bugs are characters, not warnings.** Static analyzers give you lint errors. Simulacra gives you stories. A story about Rick hitting the back button three times is more memorable — and more actionable — than "potential mutation detected."

**Silence is better than noise.** If a ghost can't find a real bug, it says so. No fabricated issues to seem thorough. The complexity gate ensures trivial code doesn't get forced theater.

**Structured output is a first-class citizen.** Every simulation produces JSON alongside the narrative. Your CI pipeline can consume it. Your issue tracker can import it. The ghosts are entertaining but the data is what ships.

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=mturac/simulacra&type=Date)](https://star-history.com/#mturac/simulacra&Date)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The easiest way to contribute:
- Add a new ghost persona
- Add a demo analysis in `skills/simulacra/demo/`
- Score an interesting SKILL.md and add the scorecard to `skills/skill-scorer/examples/`

---

## License

MIT — see [LICENSE](LICENSE).

---

<p align="center">
  <sub>Built by <a href="https://github.com/mturac">Mehmet Turac</a> — because production shouldn't be where you find your bugs.</sub>
</p>
