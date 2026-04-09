# TRIZ Brainstorming Methodology with AI Agents

> Version 2.0. A reproducible 3-round protocol for systematic inventive problem-solving.
>
> Designed for use with Claude Code's multi-agent architecture.
> Each round uses an `inventor` agent with progressively stronger anti-anchoring constraints.

---

## Key Lessons

1. **One round is NOT enough.** Anchoring bias → the agent circles around the current solution.
2. **Two rounds = minimum, three = optimal.** With each round, solution cost DROPS and persuasiveness RISES.
3. **The most valuable ideas come from the HUMAN.** The agent = amplifier, not breakthrough generator. Human sets direction, AI explores in depth.
4. **Record IMMEDIATELY** — after each round, not at the end. Context is lost.
5. **Longer top list = better.** Better to score 20 and discard 10 than to miss 1 brilliant idea.

---

## Protocol: 3 Rounds + Tracker

### Preparation (before brainstorming)

1. **Read:** Open questions, past decisions, relevant domain knowledge
2. **Write a brief** (`tmp/triz_brief_<topic>.md`):
   - Problem in one paragraph
   - What was already tried (what works / what FAILED / what's a DEAD END)
   - Current approach details (numbers, costs, timelines)
   - Main contradiction + sub-contradictions
   - Literature (key works)
   - Directions for brainstorming (5-8)
   - Constraints (budget, time, minimum viable result)
3. **Read this methodology file**

---

### Round 1: Improve the Current System

**Goal:** incremental improvements, hidden feedbacks, trimming, inversion.

**Prompt for Inventor agent:**
```
Conduct a FULL TRIZ analysis of [system X]:
1. Technical contradictions (TC) — at least 5
2. Physical contradictions (PC) — at least 3
3. 40 principles (all relevant, with specific application)
4. Su-Field models (current + transformations)
5. ARIZ (at least steps 1-3)
6. Bio-TRIZ analogues (at least 3)
7. Morphological table (5-7 axes)

Generate 15-25 hypotheses. For each:
- H-XX: title
- Essence (2-3 sentences)
- TRIZ principle
- How to verify (concrete plan)
- Assessment: cost, time, confidence (1-5)
- Risks

Categories: [A. ..., B. ..., C. ..., D. ..., E. ...]

Be BOLD and WILD! 25 wild > 5 cautious.

WebSearch is MANDATORY. Current year is 2026. Search for:
- "[specific query 1 2025 2026]"
- "[specific query 2 2025 2026]"
- ...

At the end — TOP-10 with detailed justification.

Context: [FULL description of current system, numbers, what's stuck]
Write to tmp/triz_<topic>_round1.md
```

**Output:** 15-25 hypotheses, TOP-10, saved to `tmp/triz_<topic>_round1.md`.

**Expectation:** Good incremental ideas, but locked onto current architecture.

---

### Between Rounds 1→2 (Orchestrator Analysis)

The orchestrator (main agent) does NOT wait for the human, but:

1. **Identifies bias:** Is there lock-in? All hypotheses about the same thing?
2. **Formulates bans** for Round 2: what to PROHIBIT as a basis
3. **Generates "wild" directions** on behalf of the human (at least 5)
4. **Determines specific web queries** for Round 2

---

### Round 2: Beyond the Current System

**Goal:** fundamentally new approaches. Break the anchoring bias.

**Prompt for Inventor agent:**
```
TRIZ Round 2: BEYOND [current system].

⚠️ BANNED as a basis:
- [Component X]
- [Method Y]
- [Architecture Z]
Work FROM SCRATCH.

ALLOWED: use existing DATA as input.

Ideas from the HUMAN — develop in DETAIL:
1. [Idea 1 — description]
2. [Idea 2 — description]
3. [Idea 3 — description]
4. [Idea 4 — description]
5. [Idea 5 — description]

Directions:
1. [Material/method class A]
2. [Class B]
3. [Fundamentally different architectures]
4. [Different energy/information sources]
5. [Cross-disciplinary approaches]

Be EXTREMELY BOLD! 20 wild > 5 cautious.

WebSearch is MANDATORY. Current year is 2026. Search for:
- "[query 1 2025 2026]"
- "[query 2 2025 2026]"
- ...

For each hypothesis: H-R2-XX, essence, TRIZ principle, verification,
cost/time/confidence, risk.

At the end — TOP-10 + COMPARISON with Round 1.

Context: [MINIMUM about current system, MAXIMUM about target properties]
Write to tmp/triz_<topic>_round2.md
```

**Output:** 15-25 hypotheses, TOP-10, comparison with R1, saved to `tmp/triz_<topic>_round2.md`.

**Expectation:** Some hypotheses with ideality > 1.0, new materials/methods, web-confirmed.

---

### Between Rounds 2→3 (Orchestrator Analysis)

1. **Compare R1 and R2:** what's fundamentally new?
2. **Identify most promising directions** from both rounds
3. **Formulate even stricter bans** — ban the ENTIRE CLASS of methods
4. **Prepare "human" ideas** — the wildest ones, the ones you first laugh at

---

### Round 3: Wildest Ideas + Synthesis

**Goal:** breakthrough ideas outside the discipline. Synthesis of best from R1+R2. Integration.

**Prompt for Inventor agent:**
```
TRIZ Round 3: WILDEST IDEAS + SYNTHESIS.

⚠️ BANNED as a basis:
- [Entire class of methods X — including all variants]
- [Entire class Y]
- [Everything from rounds 1 and 2 — DO NOT repeat!]

Think LIKE A HUMAN, NOT LIKE A [specialist in the current field].
Physicist, mathematician, biologist, engineer, journalist — anyone.

ALLOWED: use existing DATA.

Ideas from the HUMAN — develop in DETAIL:
1. [Wildest idea 1]
2. [Wildest idea 2]
3. [Wildest idea 3]
4. [Synthesis idea from R1+R2]
5. [Meta-idea about the process/proof/argumentation itself]

Directions (FAR from the main domain!):
1. [Information theory / mathematics]
2. [Cross-disciplinary transfer]
3. [Reformulation of the problem itself]
4. [Experimental / practical approaches]
5. [Historical analogies]

THIS IS THE LAST ROUND. HOLD NOTHING BACK. NO LIMITS ON CRAZINESS.
The best idea = the one you first laugh at, then can't stop thinking about.

WebSearch is MANDATORY. Current year is 2026. Search for:
- "[most unconventional queries 2025 2026]"
- ...

For each hypothesis: H-R3-XX, essence, TRIZ principle, verification,
cost/time/confidence, craziness-rating (1-5).

At the end:
1. TOP-10 of Round 3
2. **GRAND TOP-20 FROM ALL THREE ROUNDS** — best ideas from the entire brainstorm
3. **RECOMMENDED 48-HOUR PLAN** — concrete actions

What was already proposed in R1 and R2 (DO NOT repeat!): [brief list]
Write to tmp/triz_<topic>_round3.md
```

**Output:** 15-20 hypotheses, TOP-10 of round, GRAND TOP-20 of entire brainstorm, 48h plan.

---

### After the Brainstorm: Create a Tracker

**IMMEDIATELY** after Round 3, the orchestrator creates a tracker document:

**File:** `knowledge/Q<NNN>_<TOPIC>_TRACKER.md` (or appropriate path for your project)

**Tracker structure (required sections):**

```markdown
# Q-<NNN>: <Task name> — hypothesis tracker

> Created: <date>. Updated: <date>
> Source: TRIZ brainstorm 3 rounds, <N> hypotheses → tmp/triz_<topic>_round{1,2,3}.md
> Goal: <one sentence>
> Update: after each completed step

---

## Document Map (READ FIRST when starting a session)

| What | Path | Why |
|------|------|-----|
| Brainstorm brief | tmp/triz_brief_<topic>.md | Original problem statement |
| TRIZ Round 1 | tmp/triz_<topic>_round1.md | N hypotheses, TOP-10 |
| TRIZ Round 2 | tmp/triz_<topic>_round2.md | N hypotheses, TOP-10 |
| TRIZ Round 3 | tmp/triz_<topic>_round3.md | N hypotheses, GRAND TOP-20 |

---

## GRAND TOP-20 (from brainstorm)

| # | ID | Round | Title | Cost | Time | Confidence |
|---|-----|-------|-------|------|------|------------|
| 1 | ... | ... | ... | ... | ... | ... |

---

## Status Legend

- `[ ]` — not started
- `[~]` — in progress
- `[x]` — completed
- `[!]` — blocked (specify by what)
- `[—]` — cancelled / DEAD END

---

## Phase 0: Quick Wins (<deadline>)

> Goal: fast results to eliminate non-viable directions

### 0.1 <Step name> [<hypothesis ID>]

| # | Step | Time | Status | Result |
|---|------|------|--------|--------|
| 0.1.1 | ... | ... | [ ] | ... |

**GO/KILL:** <criterion>
**Dependencies:** <what it depends on>
```

**Tracker rules:**
- Update statuses IMMEDIATELY after each step completes
- GO/KILL criterion — MANDATORY for each block
- Dependencies — explicitly stated
- Document map — FIRST thing the next session reads

---

## Orchestration: Full Workflow

```
1. Prepare brief → tmp/triz_brief_<topic>.md
2. Launch Inventor (Round 1) → tmp/triz_<topic>_round1.md
3. Analyze R1, prepare bans and ideas for R2
4. Launch Inventor (Round 2) → tmp/triz_<topic>_round2.md
5. Analyze R1+R2, prepare wildest ideas for R3
6. Launch Inventor (Round 3) → tmp/triz_<topic>_round3.md
7. Create tracker → knowledge/Q<NNN>_<TOPIC>_TRACKER.md
8. Update open questions, decisions, literature, index
9. Write handoff
```

**All executed in ONE SESSION without waiting for approvals between rounds.**
The orchestrator performs analysis and preparation between rounds independently.

---

## What Works

### In the agent prompt
1. **Full context** — but structured (what works / dead end / numbers)
2. **Explicit hypothesis categories** — "materials", "architecture", "energy", "information"
3. **"Be BOLD and WILD"** — without this, the agent generates cautious improvements
4. **Specific output format** — "for each: title, essence, principle, verification"
5. **Specific web queries** (not "search for something")
6. **Write to file** — agent writes to tmp/, saves context
7. **Ideas from human** — agent develops them, doesn't generate breakthroughs itself

### In organization
1. **Three rounds > two > one** — each round breaks the bias of the previous
2. **Orchestrator between rounds** — analysis + idea generation + ban preparation
3. **TOP-10 after each round** — don't wait for the final
4. **GRAND TOP-20 at the end** — better to discard extras than miss something valuable
5. **Tracker IMMEDIATELY** — while context is fresh
6. **Verification scripts** — write them immediately, don't postpone

---

## What DOESN'T Work / Traps

### Anchoring bias (Round 1)
- Agent anchors to the current solution
- **Fix:** explicit ban in R2, even stricter in R3 + ideas from human

### Cautious critic (AFD)
- AFD narrows the field — agent discards "wild" ideas
- **Fix:** "25 wild > 5 cautious", craziness-rating in R3

### Shallow web search
- Without specific queries, searches for general reviews
- **Fix:** give 8-10 specific queries with years "2025 2026"

### Too much context = anchoring
- More details about current system → stronger anchoring
- **Fix:** R2 — minimum context about current, maximum about target properties. R3 — ban entire class

### Agent doesn't generate breakthroughs
- Best ideas come from the HUMAN
- Agent explores, finds literature, evaluates — but DOESN'T invent
- **Conclusion:** AI = amplifier. Human → direction, AI → exploration

### Repetition between rounds
- Without explicit list of "what was already proposed," agent repeats R1 ideas in R2
- **Fix:** in R2/R3 prompt, explicitly list ALL hypotheses from previous rounds

### Cost reduction effect
- R1 optimizes current method (expensive → cheaper)
- R2 finds alternative paths (cheaper → free)
- R3 reformulates the problem (free + more convincing)
- **Conclusion:** the third round always pays for itself — even if R2 already gave $0, R3 increases persuasiveness

---

## Effectiveness Metrics

| Metric | R1 | R2 | R3 | Target |
|--------|:---:|:---:|:---:|:---:|
| Total hypotheses | 15-25 | 15-25 | 15-20 | 50-70 per brainstorm |
| Novelty ≥ 4 | 6+ | 8+ | 10+ | > 30% |
| Novelty = 5 | 1+ | 3+ | 5+ | ≥ 1/round |
| New materials/methods | 2+ | 5+ | 3+ | ≥ 10 |
| Ideality > 1.0 | 0 | 3+ | 5+ | ≥ 5 |
| Web-confirmed | ~3 | ~10 | ~8 | > 50% |
| $0 verification cost | 2+ | 5+ | 8+ | ≥ 10 |
| TOP per round | 10 | 10 | 10 | — |
| GRAND TOP (final) | — | — | **≥ 20** | **≥ 20** |

---

## Connection to Other Skills

- **Before brainstorm:** `/insight` can reveal blind spots that inform the brief
- **After brainstorm:** `/review` can evaluate whether the chosen solution introduces new problems
- **Tracker** feeds into future brainstorms — what was tried, what failed, what worked
- **Specialist agents** (chemist, physicist, biologist, etc.) can evaluate feasibility of top hypotheses
