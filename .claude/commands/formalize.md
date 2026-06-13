# Formalize paper claims into machine-checked theorems

Turn the qualitative claims of a paper into **machine-checked theorems** where they are provable today, and honestly draw the line where they are not. The goal is a sound, sorry-free proof of the algebraic core — never an over-reached artifact that *looks* rigorous but proves something physically empty.

Current year: 2026.

## Input

Argument: a paper directory/file, or a specific set of claims. If none is given, ask which paper/section to formalize.

## Procedure

### 0. Theorem spec
Read the manuscript, its equations and tables. Write/update `THEOREM_SPEC_<topic>.md`: reduce the prose claims to statements that can be *stated precisely*.

**Run a consilium** (the `mathematician` agent + the relevant domain expert — `chemist`/`physicist`/`earth-scientist`/`biologist`). Brief them with the **verified** facts. Ask them to decide what is formalizable today, what is pen-and-paper, and what is outside any tool — and to catch **errors in the claims themselves**. Mark corrections with ⚑.

### 1. Tool triage

| Claim type | Tool |
|---|---|
| Algebra / steady state / sign of a spectrum / existence (IVT) / parity gates / discrete-combinatorial | **Lean 4 + mathlib** |
| Well-posedness / Grönwall-type bounds | **Lean 4 + mathlib** (present in mathlib) |
| Asymptotic stability / Lyapunov | **KeYmaera X (differential dynamic logic, `dI` tactic, Z3 backend)** *or* pen-and-paper (Lyapunov's first method) — mathlib has **no** stability theorems |
| Monotone / Metzler-matrix arguments | pen-and-paper |
| Large deviations / escape rates / WKB / entropy production of an irreversible network | **Do not formalize** — treat numerically; do not quote as proven |

### 2. Honest scoping (mandatory)
Prove the algebraic **core**; do not stretch. Do not create a machine-checked artifact of marginal value (the risk is a rigor-mismatch: a real proof of a physically empty statement). Explicitly partition every claim into: **machine-checked** / **pen-and-paper** / **stated without proof**.

### 3. Verification
- Run `#print axioms <theorem>` for each theorem → it must depend only on `[propext, Classical.choice, Quot.sound]`, **never** `sorryAx`.
- Re-run the whole proof from clean (reproducibility).
- Verify load-bearing **numbers** yourself. If the spec cites references, resolve each DOI before adding it to the bibliography.

### Common gotchas
- **Lean:** declare one structure field per line (multiple fields on one line silently parse as a single function-typed field); reproduce errors with a minimal import; read errors from the **head**, not the tail (the tail hides the root error). mathlib renames across versions — don't trust remembered lemma names; for division inequalities, `rw [← sub_nonneg]` + `field_simp; ring` + `div_nonneg`/`mul_nonneg` is stable.
- **KeYmaera X:** set `LC_ALL=C.UTF-8`; the `dI` tactic sidesteps the `dG` differential-ghost bug; don't port old version templates.

## Output
1. `proofs/<topic>.lean` (or `.kyx`) — green, sorry-free.
2. `proofs/VERIFICATION_REPORT.md` — what is proven, the boundary (machine-checked vs pen-and-paper vs unproven), and how to reproduce.
3. The theorem spec, annotated with what is now machine-checked.

## Rule
**An honest core beats an over-reached machine-checked artifact.** The consilium advises; the human decides the scope.
