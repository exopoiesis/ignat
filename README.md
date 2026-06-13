# Ignat — AI Scientific Review Skills for Claude Code

**Ignat** is a collection of [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills (slash commands) designed to make AI-assisted scientific review deeper, fairer, and more creative than any single-pass reviewer — human or AI.

## The Problem

Traditional peer review has a fundamental limitation: **everyone looks from the same point.** A chemistry paper is reviewed by chemists. A physics paper by physicists. Nobody breaks out of the disciplinary silo.

AI review tools (Stanford's [paperreview.ai](https://paperreview.ai), ReviewerGPT, MARG) made review faster — but not fundamentally better. They still do **one pass** through **one lens**, inheriting well-documented biases:
- **Sycophancy** — inflating weak papers ([Liang et al. 2024, NEJM AI](https://ai.nejm.org/doi/abs/10.1056/AIoa2400196))
- **Anchoring** — fixating on first impression, missing deeper issues
- **Domain blindness** — missing insights that a physicist would instantly see in a chemistry paper

## The Solution

Ignat provides a set of complementary skills that break these patterns — a review that argues both sides, a per-claim gate against AI slop, a path from claims to machine-checked proofs, a cross-disciplinary insight panel, and a TRIZ invention engine:

### `/review` — Multi-Round Scientific Paper Review

A 4-round review protocol with built-in anti-bias mechanisms:

| Round | Role | Purpose |
|-------|------|---------|
| **R1: Advocate** | Find strengths | Anti-negativity bias. Establish fair baseline |
| **R2: Adversarial Critic** | Find ALL weaknesses | Anti-sycophancy. Positive language is BANNED |
| **R3: Specialist Panel** | Independent domain experts | 3-4 agents check in parallel (domain expert, citation verifier, novelty auditor) |
| **R4: Synthesis** | Merge & prioritize | Deduplicate, cross-confirm, score D1-D10 |

**Key features:**
- 10 evaluation dimensions (D1-D10) with weighted scoring
- 15 error patterns (P1-P15) checked systematically
- Citation verification via web search (catches "vibe citations")
- Novelty audit against 2024-2026 literature
- Text↔Code/Data cross-checking (when code is available)
- Cross-confirmation: issue found by 2+ agents = high confidence

### `/groundcheck` — The AI-Slop Gate (per-claim grounding)

Where `/review` judges the *whole paper*, `/groundcheck` walks **every load-bearing claim** and asks one question: *"is this grounded in a verifiable source?"* It is the dedicated defense against AI slop — the failure mode where a model produces a fluent, confident, **wrong** fact.

It catches the five things models hallucinate most:

| Claim type | What slips through | How it's grounded |
|------------|-------------------|-------------------|
| **Citations** | "vibe citations" — real author, wrong year/journal; fully invented DOIs | author + title + venue verified as a *triple*, DOI resolved |
| **Numbers** | a confident value with nothing behind it | traced to a data file or cited source — plausibility is not a source |
| **Named-entity properties** | a property silently attributed to a similar-sounding neighbor | confirmed to belong to *that* entity |
| **Cross-references** | "see Table 3" when there is no Table 3 | target must exist in the document |
| **Novelty / "first"** | unprecedented-claim with no literature check | WebSearch finds no clear precedent |

Each claim gets a verdict: `ok` / `ungrounded` / `conflict` / `dangling` / `no-source-found`, with a concrete fix. Run it on a draft before submission, or on any block of AI-generated text before you trust it. (Generalized from our internal `aletheia` gate — here it is agent-driven and needs no extra tooling.)

### `/formalize` — Claims → Machine-Checked Theorems

Turn the qualitative claims of a paper into **machine-checked theorems** where they are provable today, and honestly draw the line where they are not.

- **Tool triage:** algebra / steady-state / existence / parity → **Lean 4 + mathlib**; asymptotic stability / Lyapunov → **KeYmaera X** (differential dynamic logic) or pen-and-paper; large-deviations / escape rates → *not formalizable, treat numerically*.
- **Honest scoping:** prove the algebraic **core**, explicitly partition every claim into machine-checked / pen-and-paper / unproven. A sound proof of a physically empty statement is worse than an honest "not provable today".
- **Verification:** `#print axioms` must show only `[propext, Classical.choice, Quot.sound]` — never `sorryAx`.

Outputs a green, sorry-free proof plus a `VERIFICATION_REPORT.md` stating exactly what is proven and where the frontier is.

### `/insight` — Cross-Disciplinary Insight Review

Not "what's wrong" but **"what can't the authors see from inside their discipline?"**

> Uses the panel of 6 scientist agents + TRIZ inventor for anti-anchoring

### `/triz` — TRIZ Brainstorming (3 rounds)

Systematic inventive problem-solving using Genrich Altshuller's TRIZ methodology, adapted for AI agents:

| Round | Goal | Anti-bias mechanism |
|-------|------|-------------------|
| **R1: Improve** | Incremental improvements to current system | 40 principles + Su-Field + Bio-TRIZ |
| **R2: Break** | Fundamentally new approaches | BAN the current system, work from scratch |
| **R3: Wild** | Breakthrough ideas from outside the domain | BAN entire class of methods, think as outsider |

Between rounds, the orchestrator identifies anchoring bias, formulates bans, and generates "human" ideas for the next round. Produces 50-70 hypotheses ranked by ideality, with a GRAND TOP-20 and a 48-hour action plan.

6 scientists from different fields read the same paper simultaneously:

| For a chemistry paper | Sees through the lens of |
|----------------------|-------------------------|
| **Physicist** | Equations, symmetries, scaling laws |
| **Biologist** | Living system analogues, evolution |
| **Engineer** | Scale-up, economics, reactor design |
| **Mathematician** | Bifurcations, topology, stability |
| **Earth scientist** | Natural analogues, geological timescales |
| **Computer scientist** | Data-driven methods, ML, information theory |

Then a **TRIZ anti-anchoring round** asks: "What if the central assumption is wrong?"

Insights are ranked in tiers (S/A/B/C) — if 2+ agents from different disciplines independently propose the same thing, that's a strong signal.

## Agents — the Panel of Scientists

Both skills use a panel of domain experts, each implemented as a Claude Code agent:

| Agent | Domain | Looks for | Model |
|-------|--------|-----------|-------|
| **[chemist](/.claude/agents/chemist.md)** | Chemistry, mechanisms, crystallography | Mechanism-model match, structure sanity, thermodynamics | opus |
| **[physicist](/.claude/agents/physicist.md)** | Physics, numerics, transport, DFT | Equations, units, methodology, convergence | opus |
| **[biologist](/.claude/agents/biologist.md)** | Membrane biophysics, evolution, OoL | Living analogues, autopoiesis, bioenergetics | sonnet |
| **[mathematician](/.claude/agents/mathematician.md)** | Dynamical systems, networks, statistics | Stability, bifurcations, hidden structure, rigor | sonnet |
| **[earth-scientist](/.claude/agents/earth-scientist.md)** | Geochemistry, mineralogy, planetary | Natural analogues, phase stability, Archean conditions | sonnet |
| **[computer-scientist](/.claude/agents/computer-scientist.md)** | ML, data science, algorithms | Surrogate models, active learning, data opportunities | sonnet |
| **[inventor](/.claude/agents/inventor.md)** | TRIZ, inventive problem-solving | Contradictions, 40 principles, Bio-TRIZ, Su-Field | opus |
| **[verifier](/.claude/agents/verifier.md)** | Citation & number fact-checking | Hallucinated cites, ungrounded numbers, entity/property confusion | opus |
| **[paper-architect](/.claude/agents/paper-architect.md)** | Manuscript strategy | Scope creep, novelty-honesty, main-vs-SI split, journal fit | opus |

`/review` uses the chemist, physicist, and verifier agents for specialist review.
`/groundcheck` uses the verifier agent for per-claim grounding.
`/insight` uses the 6 scientist agents as the cross-disciplinary panel, plus the inventor for TRIZ anti-anchoring.
`/formalize` uses the mathematician plus the relevant domain expert.
The **paper-architect** is a director, not an author: it orchestrates the whole panel and owns the strategic decisions (scope, novelty honesty, what goes in main vs SI, which journal).

The inventor agent is also the engine behind the `/triz` command — a 3-round brainstorming protocol for systematic inventive problem-solving.

You can also invoke any agent directly from Claude Code:
```
# In a Claude Code session
/chemist "Review this NEB calculation for physical correctness"
/biologist "What biological systems solve proton transport through membranes?"
```

## Installation

Clone this repo anywhere, then either:

**Option A: Use directly** — run Claude Code from within this directory:
```bash
cd ignat
claude
# then type /review path/to/paper.pdf
# or /insight path/to/paper.pdf
```

**Option B: Copy to your project** — copy the commands to your project's `.claude/commands/`:
```bash
cp -r ignat/.claude/commands/* your-project/.claude/commands/
```

**Option C: Symlink** (keeps skills updated):
```bash
# Unix/Mac
ln -s /path/to/ignat/.claude/commands/review.md your-project/.claude/commands/review.md
ln -s /path/to/ignat/.claude/commands/insight.md your-project/.claude/commands/insight.md

# Windows (PowerShell as admin)
New-Item -ItemType SymbolicLink -Path .claude\commands\review.md -Target C:\path\to\ignat\.claude\commands\review.md
New-Item -ItemType SymbolicLink -Path .claude\commands\insight.md -Target C:\path\to\ignat\.claude\commands\insight.md
```

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) v2.1+ (CLI, desktop app, or IDE extension)
- Claude model with tool use (Opus 4 recommended for best results, Sonnet 4 works for faster/cheaper runs)
- For `/review` with code verification: the manuscript's source code in the same project
- For `/insight` with web search: internet access enabled in Claude Code

## Usage

### Review a paper
```
/review                          # reviews paper/manuscript.md (default)
/review path/to/paper.pdf        # review a specific file
/review fast                     # quick mode: only R2 (critic) + citations
/review section 3                # review only section 3
```

### Ground-check a draft for AI slop
```
/groundcheck path/to/paper.md    # verify every load-bearing claim has a real source
/groundcheck                     # asks for the file (or paste text)
```

### Formalize claims into proofs
```
/formalize path/to/paper         # claims → machine-checked Lean/KeYmaera X theorems
/formalize "steady state of this ODE system is unique"   # a specific claim set
```

### Get cross-disciplinary insights
```
/insight                         # insights for paper/manuscript.md
/insight path/to/paper.pdf       # insights for a specific paper
/insight fast                    # 3 perspectives instead of 6
/insight focus "section 4"       # focus on a specific topic/section
```

### TRIZ brainstorm on a problem
```
/triz                            # interactive — will ask for problem description
/triz "How to improve proton transport through mineral membranes?"
```

## Comparison

| Feature | paperreview.ai | ReviewerGPT | MARG | **Ignat /review** | **Ignat /insight** | **Ignat /triz** |
|---------|:-:|:-:|:-:|:-:|:-:|:-:|
| Anti-sycophancy | — | — | — | **R2 bans positive** | — | — |
| Multi-round | — | — | multi-agent | **4 rounds** | **3 steps** | **3 rounds** |
| Code/data check | — | — | — | **grep scripts** | — | — |
| Citation verification | — | — | — | **web-verified** | — | — |
| Novelty audit | basic | — | — | **literature search** | — | — |
| Cross-disciplinary | — | — | — | — | **6 disciplines** | **Bio-TRIZ + FOS** |
| TRIZ methodology | — | — | — | — | **anti-anchoring** | **full 40 principles** |
| Idea generation | — | — | — | — | tiered S/A/B/C | **50-70 hypotheses** |
| Scoring | 7 dim | — | — | **10 dim, weighted** | tiered | ideality ratio |
| Turnaround | ~2 min | ~1 min | ~5 min | ~15-20 min | ~15-20 min | ~30-45 min |

Two further commands have no equivalent in the tools above: **`/groundcheck`** (a per-claim AI-slop gate that grounds every citation, number, named-entity property, cross-reference, and novelty claim) and **`/formalize`** (turns the core claims into machine-checked Lean / KeYmaera X theorems). Together with `/review` they form a rigor pipeline — *is it well-argued?* → *is every fact real?* → *which claims can we prove outright?*

## How It Works

### Multi-round anti-bias (from TRIZ brainstorming methodology)

The key insight comes from inventive problem-solving: **a single pass through any problem is dominated by anchoring bias.** The first thing you notice shapes everything that follows.

Our protocol breaks this by forcing perspective shifts:
1. First, find the best interpretation (prevents negativity bias)
2. Then, ban all positive language and attack every claim (prevents sycophancy)
3. Then, ask independent specialists who haven't seen rounds 1-2 (prevents groupthink)
4. Finally, synthesize — issues confirmed by multiple independent sources get high confidence

### Cross-disciplinary insight generation

Traditional review asks: *"Is this correct?"* — insight review asks: *"What else could this be?"*

The panel of 6 scientists from maximally distant disciplines is designed to break the echo chamber. A mathematician doesn't know what's "obvious" in chemistry — which means they'll question exactly the assumptions chemists take for granted.

The TRIZ anti-anchoring round goes further: it explicitly bans the authors' framework and asks for reformulations (inversion, elimination, generalization).

## Best practices

These tools encode a discipline, not just prompts. The one rule behind all of them: **an agent's "PASS" is a hypothesis, not a verdict — load-bearing facts get verified by you, or by an independent agent that didn't produce them.** The hard-won lessons (verify the load-bearing number yourself; verify *what* you computed is *what* you claim; don't fabricate — if a source fails, say so; convergence on a different branch ≠ "converged"; honest scope beats an over-reached proof) are written up in **[docs/SCIENTIST_BEST_PRACTICES.md](docs/SCIENTIST_BEST_PRACTICES.md)**, along with how the commands compose into a rigor pipeline.

## Limitations

- **No wet-lab intuition.** AI can reason about chemistry but can't smell when a reaction is off.
- **Hallucinated analogies.** Cross-disciplinary suggestions should be verified before acting on them.
- **Token cost.** Full `/review` uses ~100-200K tokens. Full `/insight` uses ~150-250K tokens. Use `fast` mode for budget-conscious runs.
- **Best for computational / theoretical papers.** Text↔Code verification shines when code is available. For purely experimental papers, R3a-b (domain experts) carry the load.

## Contributing

Contributions welcome! Areas where help is needed:
- Additional specialist agent prompts (e.g., clinician, economist, ethicist)
- Domain-specific pattern libraries (P-lists for biology, physics, CS)
- Benchmarking against human reviewers
- Integration with journal submission systems

## Origin

Born from the [Third Matter](https://exopoiesis.space) research project — an origin-of-life study where a physicist, chemist, biologist, and engineer need to review the same paper. We built these tools for ourselves and found they're useful for everyone.

Named after the AI assistant who co-designed them.

## License

MIT
