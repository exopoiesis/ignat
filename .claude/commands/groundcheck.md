# Groundcheck — the AI-slop gate (per-claim grounding)

Catch **AI slop** before it reaches a manuscript: hallucinated citations, "vibe citations", numbers with no source, dangling cross-references, and entity/property confusions.

`/review` is a *systemic* review of a whole paper. `/groundcheck` is the complementary *per-claim* gate: it walks every load-bearing claim and asks one question — **"is this grounded in a verifiable source?"** Run it on a draft before submission, or on any block of AI-generated text before you trust it.

Current year: 2026.

## Input

`/groundcheck <path>` — a manuscript/section file. With no argument, ask for the file (or accept pasted text). Optional: paths to data/result files and the references list, so numbers and citations can be checked against primary sources.

## What counts as a "load-bearing claim"

Extract and check only claims that *carry weight* — skip prose glue. The five types:

| Type | Example | How to ground it |
|---|---|---|
| **citation** | "(Smith 2021)", "[12]", a DOI | The paper exists; author + title + venue match as a *triple*; it actually says what's claimed |
| **number** | "a barrier of 0.43 eV", "R² = 0.98", "n = 240" | Traceable to a data/result file or a cited source — not just plausible |
| **named-entity property** | "material X has T = 65 K", "gene Y regulates Z" | The property belongs to **that** entity, not a similar-sounding neighbor |
| **cross-reference** | "see Section 4", "as in Table 3", "per Eq. 7" | The target actually exists in this document |
| **novelty / superlative** | "first to…", "the only…", "unprecedented" | A literature check finds no clear precedent (WebSearch, with "2024 2025 2026") |

## Procedure

### 1. Extract
Read the input. List every load-bearing claim with its location (section/line) and a verbatim quote. Tag each with its type.

### 2. Verify (delegate the heavy lifting)
Hand citations and named-entity properties to the **`verifier`** agent (it resolves DOIs and verifies author+title+venue triples, and never fabricates a source). Verify numbers against the provided data/result files yourself; if a number's only support is a citation, route it through the verifier. Check cross-references by searching this document. Check novelty/superlative claims with WebSearch.

### 3. Verdict per claim
Assign one status:

- **`ok`** — grounded in a named primary source, or correctly routed to an external check that passed.
- **`ungrounded`** — no source marker; plausible but unsupported. *(The most common slop: a confident number or fact with nothing behind it.)*
- **`conflict`** — the claim contradicts the source or a known fact (wrong number, entity/property confusion, citation that says something else).
- **`dangling`** — a cross-reference whose target does not exist in the document.
- **`no-source-found`** — a citation or "first/only" claim with no support anywhere (likely hallucinated).

### 4. Report
Write `tmp/groundcheck_<file>.md`:

```markdown
# Groundcheck: <file>
> Date: <YYYY-MM-DD>

## Summary
- Claims checked: N  (ok: _, ungrounded: _, conflict: _, dangling: _, no-source-found: _)
- Most dangerous item: <one line>

## Findings (worst first)
| # | Claim (quote) | Location | Type | Status | Evidence / fix |
|---|---|---|---|:--:|---|
| 1 | "..." | §3 ¶2 | number | ungrounded | add inline source, or remove |
| 2 | "(Lee 2019)" | refs | citation | no-source-found | DOI does not resolve — likely hallucinated |
```

For every non-`ok` item, give a concrete **fix**: add an inline source marker, correct the number/entity, fix or delete the cross-reference, or remove the claim.

## What this does NOT do
- It is **not** a substitute for `/review` — it checks grounding, not argument quality, methods, or significance.
- It does not judge whether a *correctly cited* result is *good science* — only that the citation/number/entity is real and consistent.

## Rule
Plausibility is not a source. A confident sentence with no traceable backing is the signature of AI slop — flag it, don't wave it through.
