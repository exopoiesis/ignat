# Consilium — adversarial design + execution review panel (you assemble the brief)

An on-demand adversarial panel for **one specific question**: an input before you run it, a claim before you ship it, a result before you trust it, or a choice between approaches. You pick the panel, write the brief, run the experts in parallel, and synthesize the verdict.

How it differs from the other skills:
- `/review` is a *systemic, whole-paper* 4-round review.
- `/insight` *generates* cross-disciplinary ideas.
- `/consilium` *vets* a single artifact or decision — and it leads with the layer the others skip.

Current year: 2026.

## Input

`/consilium <target>` — a question, a file, an input-before-deploy, a claim, or an approach/design choice. If empty, ask what to vet (one line).

## ⚠️ Two layers. Layer 1 ALWAYS runs first — even when asked only "check this input."

Most review — human or AI — is **execution-QA**: *"is this calculation/argument correct?"* (inputs, numbers, convergence, units, crystallography). It systematically misses **design-QA**: *"is this the RIGHT calculation?"* — the framing question. The hardest errors are not wrong answers to the right question; they are flawless answers to the wrong one. So:

### Layer 1 — DESIGN-ADEQUACY (framing-QA). Answer yourself, then put to the panel:
1. **Is the observable / metric / coordinate field-standard?** Is it a custom proxy where the field has an established measure? A one-body proxy for a many-body quantity? Look for **method-precedent** — how does the field actually *measure* this — not just for facts.
2. **Null hypothesis / identifiability.** What is the trivial / flat alternative, and has it been tested or ruled out? Is the effect even *distinguishable* from zero given the data at hand?
3. **The cheapest control that would FALSIFY the claim — has it been designed?** Every caveat should become an active control, not a passive disclaimer.
4. **Is there a cheaper observable / path to the REAL question?** Rabbit-hole check — mandatory **after ≥2 failures of an expensive attempt** or **before any expensive commitment**. In-session momentum anchors on the chosen path and optimizes *within* it.
5. **What does an analogous, well-studied problem use?**

### Layer 2 — EXECUTION-QA (after Layer 1):
The concrete artifact: correctness, numbers, convergence, units, edge cases, internal consistency, method-labels-vs-output, anything that bites at run time.

## Choosing the panel (you decide — don't ask)

- **Default:** `chemist` + `physicist` (or the two closest domain experts).
- **Add by topic:** `mathematician` (identifiability, stability, statistics, rigor, formal proof), `computer-scientist` (ML, data-driven shortcuts, sampling, information limits), `biologist` (living-system analogues, origin-of-life), `earth-scientist` (geochemistry, natural analogues, phase stability), `inventor` (when the answer is "design a better experiment"), `paper-architect` (scope / narrative / main-vs-SI), `verifier` (citations / numbers).
- **For framing / design questions, ALWAYS include ≥1 lens OUTSIDE the paper's home discipline** (e.g. `mathematician` or `computer-scientist` on a chemistry question). Framing blind spots live exactly where everyone shares the same training. The default chem+phys pairing is where they hide — don't let those two lenses run alone on a framing question.
- 2–4 experts; more only if the question is genuinely multi-domain.

## ⚖️ The Calibrator lens (anti-Reviewer #2) — for finalizing / revising a manuscript

Adversarial review hunts **over-claiming and hidden weakness** → it pushes toward *over-disclosure*. There is an opposite, under-watched defect: **over-honesty** — hedging a result *below* the strength its evidence supports, foregrounding a non-material caveat in every section, stating a bare weakness with no bound, or arming the reviewer with hypothetical objections. The **Calibrator** is a distinct lens with the **opposite target**: where the manuscript *shoots itself in the foot with honesty*. It is **still adversarial** (it hunts a defect — self-sabotage), so it doesn't break stance; but it MUST be a **separate agent** from Reviewer #2 (one agent told to be "harsh and gentle" at once is incoherent).

**When to include:** finalizing/revising a manuscript, before submission. NOT in a default execution consilium.

**Principle.** Disclosing a material limitation is a **binary duty**; its **framing is a free variable**. The honesty test for any reframe: *would a competent reader form a CORRECT belief about the strength of the claim?* If yes → the framing is legitimate however favourable. If a favourable framing would make them **over**estimate → that is the line, don't cross it. The Calibrator optimizes the presentation of **what we believe is true**; it never argues for what we believe is false.

**Brief to the Calibrator (5 levers — find each, propose the concrete reframe):**
1. **Scope vs limitation:** "we couldn't compute X" (incompetence frame) → "X is outside this work's scope / addressed in follow-up" (open-frontier frame). Same fact.
2. **State once:** a limitation repeated in N sections reads as N weaknesses. Consolidate into Limitations; reference it elsewhere.
3. **Caveat + mitigation:** a bare "X is uncertain" invites attack; "X is uncertain, **but** the qualitative conclusion is robust because Y" closes the loop.
4. **Claim-first:** lead with what you HAVE; the caveat is a footnote, not the opening.
5. **Verb calibration:** `suggests → consistent with → provides evidence for → establishes → demonstrates`. Over-honesty = a verb *below* the evidence. Match it exactly.
   + **Don't arm the reviewer:** never invent a hypothetical objection no reviewer would raise (brainstorm those privately, not in the text).

**⚠️ Calibrator guard-rail:** it may NOT push toward over-claiming. Every proposed reframe passes the same "correct reader belief" test. (No special tooling needed — this is an agent-driven judgement pass; mechanical hedge/repetition scans can pre-feed candidates if you have them, but the lens is the judge.)

**Calibrator deliverable:** a list of "where we sold ourselves short" (under-claim / over-disclosure / self-sabotage), each with a CONCRETE reframe and a class — **BLOCKER** (distorts the reader's perception of claim strength) vs **nice-to-have** — plus an explicit list of hedges it would NOT touch (the *earned* ones, where the verb already matches the evidence).

## Procedure

1. **You write the brief** (context; the exact question; Layer-1 + Layer-2 questions; which files/data to read; the deliverable). Save it to `tmp/consilium_<topic>.md`.
2. **Run the experts in PARALLEL** (one message, several agent calls), in **Reviewer #2 / adversarial** mode: "do not rubber-stamp OK", find the weakest link, **verify load-bearing claims yourself** (by re-derivation / primary source / re-count), cite specifics. If you are vetting an expert's prior verdict, hand them their prior file and say "treat it as refuted if the data disagree."
3. Each expert writes a verdict file `tmp/consilium_<topic>_<role>.md` and returns a summary.
4. **You synthesize:** merge / deduplicate; **recompute every load-bearing number yourself** (charge, multiplicity, electron count, a stated ΔBIC, a "code X can't do Y" feature-absence — opus-grade experts make confident slips on load-bearing numbers); classify BLOCKER vs nice-to-have; give ONE recommendation.
5. **Record the verdict** where it belongs.

## Rules

- **Adversarial by design** — praise without a specific claim is banned.
- **Trust the expert's DIRECTION, recompute the NUMBER yourself** (load-bearing only). The same discipline as "agents write PASS on real bugs" — applied up the ladder of expertise.
- **BLOCKER-before-deploy ≠ nice-to-have** — separate them explicitly.
- **Token economy:** 2–4 experts, tight briefs, parallel. Don't spawn rounds without need.
- When searching the literature, tell sub-agents the current year explicitly and to include the latest work.
