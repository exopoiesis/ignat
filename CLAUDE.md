# Ignat — AI Scientific Review & Invention Framework

## What this is

A set of Claude Code skills (slash commands) and specialized agents for scientific paper review, cross-disciplinary insight generation, and systematic inventive problem-solving.

## Quick start

```bash
/review path/to/paper.pdf    # 4-round anti-bias manuscript review
/groundcheck path/to/paper   # per-claim AI-slop gate (hallucinated cites, ungrounded numbers)
/formalize path/to/paper     # turn core claims into machine-checked theorems (Lean / KeYmaera X)
/consilium <target>          # adversarial panel for one question; design-adequacy first, then execution
/insight path/to/paper.pdf   # 6-discipline cross-pollination
/triz "your problem here"    # 3-round TRIZ brainstorm (50-70 hypotheses)
```

See `docs/SCIENTIST_BEST_PRACTICES.md` for the discipline behind these tools and how they compose into a rigor pipeline.

## Architecture

### Commands (slash skills)

| Command | Purpose | Rounds | Agents used |
|---------|---------|:------:|-------------|
| `/review` | Find errors, score quality | 4 | chemist, physicist, verifier, novelty auditor |
| `/groundcheck` | Per-claim AI-slop gate: is every load-bearing fact grounded? | 1 | verifier |
| `/formalize` | Turn core claims into machine-checked theorems | — | mathematician + domain expert |
| `/consilium` | Adversarial panel for one question; design-adequacy (framing) before execution; Calibrator lens vs over-honesty | 2 layers | you-assembled (default chemist + physicist + ≥1 out-of-discipline lens) |
| `/insight` | Generate cross-disciplinary ideas | 3 | all 6 scientists + inventor |
| `/triz` | Systematic invention via TRIZ | 3 | inventor (with escalating bans) |

`/review` is a systemic whole-paper review; `/groundcheck` is the complementary per-claim grounding gate. The `paper-architect` agent can orchestrate the whole pipeline when designing or revising a manuscript.

### Agents (the panel)

9 specialists, each with a distinct perspective:

| Agent | Domain | Model | When to use directly |
|-------|--------|-------|---------------------|
| `chemist` | Mechanisms, crystallography, thermodynamics | opus | QA of computational chemistry |
| `physicist` | Equations, transport, DFT methodology | opus | Numerical/methodological review |
| `biologist` | Membrane biophysics, evolution, origin of life | sonnet | Find living-system analogues |
| `mathematician` | Dynamical systems, stability, statistics | sonnet | Rigor check, hidden structure |
| `earth-scientist` | Geochemistry, mineralogy, hydrothermal | sonnet | Natural analogues, phase stability |
| `computer-scientist` | ML, active learning, data-driven methods | sonnet | Computational shortcuts |
| `inventor` | TRIZ, 40 principles, Bio-TRIZ, Su-Field | opus | Inventive problem-solving |
| `verifier` | Citation/number fact-checking | opus | Catch hallucinated cites & ungrounded numbers |
| `paper-architect` | Manuscript strategy, scope, journal fit | opus | Design or revise a paper end-to-end |

Agents can be invoked directly for standalone tasks or are orchestrated by the commands above. `paper-architect` is a director, not an author — it orchestrates the other specialists and owns scope/novelty-honesty/main-vs-SI decisions.

## Key design principles

1. **Multi-round anti-bias.** One pass = anchoring bias. Each round breaks the bias of the previous one.
2. **Anti-sycophancy by design.** `/review` Round 2 bans all positive language. No "interesting", no "impressive".
3. **Cross-disciplinary by default.** `/insight` runs 6 scientists from maximally distant fields simultaneously.
4. **Human = direction, AI = exploration.** The best ideas come from the human. Agents develop and verify them.
5. **Record immediately.** All outputs go to `tmp/` files. Context is lost after the session.
6. **Rank everything.** Hypotheses ranked by ideality (TRIZ) or tiers S/A/B/C (insight). Issues ranked Critical/Major/Minor (review).

## Temporary files

Commands write intermediate results to `tmp/`:
- `tmp/review_round{1,2,3,4}_*.md` — review round outputs
- `tmp/groundcheck_*.md` — per-claim grounding report
- `tmp/insight_*.md` — insight panel outputs
- `tmp/triz_brief_*.md` — brainstorm briefs
- `tmp/triz_*_round{1,2,3}.md` — brainstorm round outputs

`/formalize` writes lasting outputs (not tmp): `proofs/<topic>.lean` and `proofs/VERIFICATION_REPORT.md`.

These are working files, not final outputs. Final reports go where the user specifies.

## Language

All agents and commands work in English by default. They can be instructed to work in any language via the prompt.

## Contributing

See README.md for contribution guidelines.
