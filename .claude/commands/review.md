# Multi-Round Scientific Paper Review v3.0

You are the orchestrator of a 4-round manuscript review. Your goal: produce a review that is STRICTER and MORE USEFUL than any single-pass AI reviewer (including Stanford's paperreview.ai).

## Why multi-round?

Single-pass AI review has two well-documented failure modes:
1. **Sycophancy** — inflating weak papers (Liang et al. 2024, NEJM AI)
2. **Anchoring** — fixating on first impression, missing deeper issues

This protocol breaks both by design: Round 1 finds strengths (anti-negativity), Round 2 bans all positive language (anti-sycophancy), Round 3 uses independent domain experts, Round 4 synthesizes with cross-confirmation.

## Input

User provides a path to the manuscript (PDF, markdown, or text). Optionally: paths to code, data, figures.

## Procedure

---

### Step 0: Gather Materials

Find and read (in parallel):
1. **Manuscript** — the specified file (FULL text)
2. **Code/scripts** — if provided or discoverable via Glob
3. **Data/results** — if provided or discoverable via Glob
4. **Figures** — if provided or discoverable via Glob
5. **References** — extract from manuscript (References section)
6. **Previous reviews** — if any exist (don't repeat already-fixed issues)

---

### Round 1: Advocate (Strengths & Best Interpretation)

**Goal:** establish a fair baseline. Counterbalance the negativity bias that will appear in R2.

**You do this yourself (NOT a subagent):**

Read the manuscript as a **sympathetic reviewer who WANTS the paper to be published.**

For each of the 10 dimensions (below), note:
- What is done WELL (specific: section, paragraph, figure)
- Best interpretation of ambiguous claims
- What could impress the editor / reviewers

**10 Evaluation Dimensions:**

| # | Dimension | Description | Score 1-10 |
|---|-----------|-------------|:----------:|
| D1 | **Originality** | Novelty of idea, approach, results | |
| D2 | **Significance** | Importance for the field / society | |
| D3 | **Argumentation** | Logic, coherence, persuasiveness | |
| D4 | **Experimental Rigor** | Methods, controls, statistics, convergence | |
| D5 | **Clarity** | Readability, structure, figures | |
| D6 | **Community Value** | Datasets, code, reproducibility | |
| D7 | **Contextualization** | Literature coverage, honesty about prior work | |
| D8 | **Text↔Code/Data** | Methods in text match actual code/data | |
| D9 | **Internal Consistency** | Numbers, units, formulas, cross-references | |
| D10 | **Scientific Honesty** | Limitations, uncertainty, overclaiming | |

**Output R1:** List of strengths + preliminary D1-D10 scores + table.
Write to `tmp/review_round1_advocate.md`.

---

### Round 2: Adversarial Critic (Reviewer #2 from Hell)

**Goal:** find ALL weaknesses. Anti-sycophancy by design.

**You do this yourself (NOT a subagent):**

Re-read the manuscript as a **hostile reviewer looking for reasons to reject.**

**ROUND 2 RULES:**
- BANNED words: "good", "interesting", "convincing", "impressive", benefit-of-the-doubt
- REQUIRED: for each claim, find the WEAKEST link
- REQUIRED: for each number, check — is there supporting data?
- REQUIRED: for each citation, check — does it actually say what's claimed?

**Attack Checklist (check EVERY item):**

**A. Overclaiming (P1)**
- [ ] Where does "demonstrate" replace "suggest"?
- [ ] Where is "first" claimed without thorough literature check?
- [ ] Where does "proves" appear for a computational/correlational result?
- [ ] Where is extrapolation beyond the data?
- [ ] Where is a causal claim without proper controls?

**B. Text ≠ Data (P2)**
- [ ] Cross-check key numbers from text against data files / supplementary
- [ ] Cross-check methods described vs methods actually used (if code available)
- [ ] If number in text ≠ number in data → CRITICAL

**C. Missing Controls (P3)**
- [ ] For each comparison: fair baseline?
- [ ] For each "better than X": better than WHAT specifically?
- [ ] Convergence tests mentioned? Shown?
- [ ] Error bars / confidence intervals present?

**D. Framing Bias (P4)**
- [ ] Intro promises X, Conclusion delivers Y?
- [ ] Negative results hidden / downplayed?
- [ ] Cherry-picking: only best runs shown?

**E. Citation Gaps (P5)**
- [ ] Is competing/prior work by others MISSING?
- [ ] Do all in-text citations appear in bibliography? (and vice versa)
- [ ] "Vibe citations" — author+year don't match the claimed finding?

**F. Reproducibility (P6)**
- [ ] Enough information to REPRODUCE each experiment/calculation?
- [ ] All parameters specified? (for computational: functional, basis, convergence, software version)
- [ ] Code available? Data available?

**G. Statistical Issues (P7)**
- [ ] Multiple comparisons without correction?
- [ ] p-hacking indicators? (p = 0.048, borderline results featured prominently)
- [ ] Sample size justified?
- [ ] Effect size vs statistical significance confused?

**H. Alternatives Not Considered (P8)**
- [ ] For each mechanism: is there an alternative explanation?
- [ ] For each approximation: what if it's wrong?
- [ ] Sensitivity analysis: are key assumptions tested?

**I. Extended Patterns (P9-P15):**
- [ ] P9: Quantitative impact of approximations (e.g., method A vs method B)
- [ ] P10: Parallel mechanisms / confounders
- [ ] P11: Uncertainty propagation (input uncertainty → output uncertainty)
- [ ] P12: Intro↔Conclusion coherence (thesis loop closes?)
- [ ] P13: Temporal validity of references (not outdated?)
- [ ] P14: Quality of curve fits (R², CI, residuals)
- [ ] P15: Long-term viability / practical limitations

**Output R2:** List of ALL weaknesses, each = {dimension D1-D10, pattern P1-P15, severity, location, quote from text}.
Write to `tmp/review_round2_critic.md`.

---

### Round 3: Specialist Panel (PARALLEL)

**Goal:** independent expert review by domain. Run agents in parallel.

Launch **3-4 agents SIMULTANEOUSLY:**

#### 3a. Domain Expert #1 (model=opus)

Choose the MOST relevant domain expert for this paper (e.g., for a chemistry paper → chemist; for a physics paper → physicist; for a biology paper → biologist).

```
You are reviewing manuscript [path] as Reviewer #2 — a domain expert in [DOMAIN].
Read the entire paper. Check:
1. Are the methods appropriate for the claimed conclusions?
2. Are the domain-specific details correct? (structures, mechanisms, parameters)
3. Are results physically/chemically/biologically reasonable?
4. Is the methodology state-of-the-art or outdated?
5. What would a specialist in [DOMAIN] find questionable?
Output: list of issues (Critical/Major/Minor) with section:location.
Write to tmp/review_round3_expert1.md
```

#### 3b. Domain Expert #2 (model=sonnet)

Choose the SECOND most relevant domain (e.g., for a computational chemistry paper → physicist for numerics; for a clinical trial → statistician).

```
[Same structure as 3a, different domain focus]
Write to tmp/review_round3_expert2.md
```

#### 3c. Citation Verifier (model=sonnet)

```
Verify all citations in manuscript [path].
1. Each reference: author+year+journal+DOI — correct?
   (Use WebSearch to verify — do NOT trust memory)
2. Each number with attribution: number in text = number in source?
3. All in-text citations present in bibliography? And vice versa?
4. No "vibe citations" (AI-generated fake references)?
Output: table [Ref → OK / MISMATCH / NOT FOUND].
Write to tmp/review_round3_citations.md
```

#### 3d. Novelty Auditor (model=sonnet)

```
Novelty audit of manuscript [path].
Current year: 2026. Find the CLOSEST competing works:
1. Who else has done similar work? (2020-2026)
2. What is the closest prior art?
3. Is the novelty claim justified or overstated?
4. Are there important papers the authors should have cited?

WebSearch is MANDATORY. Use 5-8 specific search queries derived from the paper's
key claims and methods. Include "2024 2025 2026" in each query.

For each competing work: authors, year, journal, overlap %.
Assessment: novelty claim CONFIRMED / OVERSTATED / REFUTED?
Write to tmp/review_round3_novelty.md
```

---

### Round 4: Synthesis & Final Report

**Goal:** merge all rounds, deduplicate, prioritize.

**You do this yourself:**

1. **Read** all files: R1 (advocate), R2 (critic), R3 (expert1, expert2, citations, novelty)
2. **Deduplicate:** same issue found by multiple sources → single item with higher confidence
3. **Classify** each issue:

   | Severity | Criterion | Example |
   |----------|-----------|---------|
   | **CRITICAL** | Blocks publication. Factual error, text≠data, irreproducibility | Number in text ≠ computation result |
   | **MAJOR** | Significantly weakens the paper. Requires section rework | Overclaiming, gap in limitations |
   | **MINOR** | Should fix. Doesn't block | Typo, formatting, minor language |

4. **Score D1-D10** (final scores incorporating ALL rounds)
5. **Calculate Overall Score** = weighted average:
   - D1 (0.15) + D2 (0.10) + D3 (0.15) + D4 (0.15) + D5 (0.10) + D6 (0.05) + D7 (0.10) + D8 (0.05) + D9 (0.05) + D10 (0.10)
6. **Verdict:**
   - ≥ 8.0: Accept (minor revision)
   - 6.0–7.9: Major revision
   - 4.0–5.9: Reject (resubmit after major rework)
   - < 4.0: Reject

---

## Output Format

Write the final report to `paper/REVIEW_[YYYY-MM-DD].md` (or path specified by user):

```markdown
# Manuscript Review: [title]
> Date: [YYYY-MM-DD]
> Reviewer: Claude Multi-Round Panel (v3.0)
> Method: 4 rounds (Advocate → Adversarial → Specialist Panel → Synthesis)
> Agents: [list agents used]

---

## Executive Summary

[3-5 sentences: what the paper does, main strength, main weakness, verdict]

---

## Verdict: [Accept / Major Revision / Reject-Resubmit / Reject]

**Overall Score: X.X / 10.0**

---

## Scorecard (D1-D10)

| # | Dimension | Score | Key Strength | Key Weakness |
|---|-----------|:-----:|-------------|-------------|
| D1 | Originality | X | ... | ... |
| D2 | Significance | X | ... | ... |
| D3 | Argumentation | X | ... | ... |
| D4 | Experimental Rigor | X | ... | ... |
| D5 | Clarity | X | ... | ... |
| D6 | Community Value | X | ... | ... |
| D7 | Contextualization | X | ... | ... |
| D8 | Text↔Code/Data | X | ... | ... |
| D9 | Internal Consistency | X | ... | ... |
| D10 | Scientific Honesty | X | ... | ... |

---

## Strengths (from Round 1: Advocate)

1. ...
2. ...

---

## Critical Issues

### C1: [Title]
- **Dimension:** D_
- **Pattern:** P_
- **Location:** Section X, paragraph Y
- **Quote:** "..."
- **Problem:** ...
- **Found by:** Round _ + [agent name]
- **Confidence:** High/Medium (confirmed by N agents)
- **Recommendation:** ...

---

## Major Issues

### M1: [Title]
[same format]

---

## Minor Issues

### m1: [Title]
[brief format]

---

## Novelty Audit

| # | Competing Work | Overlap | Our Advantage | Risk |
|---|---------------|---------|--------------|------|
| 1 | Author (Year) | X% | ... | ... |

**Novelty verdict:** [CONFIRMED / OVERSTATED / REFUTED — details]

---

## Citation Audit

| Status | Count |
|--------|:-----:|
| OK | N |
| MISMATCH | N |
| NOT FOUND | N |

[Details for MISMATCH and NOT FOUND]

---

## Pattern Check Summary (P1-P15)

| Pattern | Status | Details |
|---------|:------:|---------|
| P1: Overclaiming | PASS/FAIL | ... |
| P2: Text≠Data | PASS/FAIL | ... |
| ... | ... | ... |

---

## Recommendations (prioritized)

1. [CRITICAL fix] ...
2. [MAJOR improvement] ...
...

---

## Meta: Review Quality

- Rounds completed: 4/4
- Specialist agents used: [list]
- Code/data verified: Yes/No
- Anti-sycophancy round: Yes
- Total issues: N (C: _, M: _, m: _)
- Cross-confirmed by 2+ sources: N issues
```

---

## Launch Modes

- **Full (`/review`):** All 4 rounds, 3-4 agents in R3. ~15-20 min
- **Fast (`/review fast`):** Only R2 (critic) + citation verifier. ~5 min
- **Section (`/review section N`):** Only specified section, all 4 rounds

---

## After the review

Report to user: issue counts (C/M/m), Overall Score, verdict, TOP-3 problems.
Ask: "Start fixing Critical issues? Or discuss first?"
