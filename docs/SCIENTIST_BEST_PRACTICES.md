# Working with AI agents in science — hard-won best practices

These are the lessons that cost us the most to learn while doing real computational science with AI agents (DFT, kinetics, ML potentials, manuscript writing). None of them are about prompting tricks; they are about **not letting a confident agent put a wrong number into a paper**. The Ignat commands (`/review`, `/groundcheck`, `/formalize`, `/insight`, `/triz`) operationalize most of them — this doc is the *why*.

## The one rule behind all the others

> **An agent's "PASS" is a hypothesis, not a verdict.** Load-bearing facts get verified by *you* — or by an independent agent that didn't produce them.

Agents are excellent at exploration and terrible at admitting they don't know. The failure mode is not random noise; it is a *fluent, plausible* wrong answer. Everything below is a defense against that.

---

## 1. Verify the load-bearing number yourself
An agent will write "PASS" over a real bug. We have repeatedly seen a script with a critical error return a number *in a plausible range* — coincidence reads as correctness. For any number that ends up in a figure, table, or abstract: re-derive it, or check the mechanism that produced it, not just its magnitude. Do this **only** for load-bearing values — not every intermediate.

## 2. Verify what you computed is what you claim (object identity)
Before trusting a result, confirm the object it was computed on *is* the object named in the claim — by its **content**, not its filename or folder label. (We once spent days of compute on a pristine structure sitting in a folder named for a defect, because a build step silently loaded the wrong file.) Check composition, count, and a diff-against-baseline — a label is not evidence.

## 3. Don't fabricate; if the source fails, ask
If a web fetch fails or a fact can't be found, the correct output is "I couldn't verify this" — never a plausible invention. This applies doubly to citations: AI-generated references mix real authors with wrong years/journals. Verify **author + title + venue as a triple**, and resolve the DOI, before any reference enters a bibliography. (`/groundcheck` and the `verifier` agent automate this.)

## 4. A "PASS" from a subagent is not QA
Subagents skim. They miss code far from the point they grepped; they rubber-stamp structurally wrong inputs. For anything that matters, the only reliable test is a **controlled A/B** (change one thing, see if the result moves as predicted). Cross-confirmation by *independent* agents (the basis of `/review` Round 3) beats one agent's confidence.

## 5. Convergence on a different branch ≠ "converged"
If a sensitivity/convergence test was run under different settings than the number you quote (a different method, parameter, or regime), you cannot claim the quoted number is converged. Say "established on branch A, *assumed transferable* to branch B" and name the dominant remaining uncertainty — or re-run on the branch you actually quote.

## 6. One pass = anchoring bias
The first thing you (or the agent) notice shapes everything after it. A single review pass inflates weak work (sycophancy) and fixates on first impressions (anchoring). Force perspective shifts: find strengths first, then **ban all positive language** and attack every claim, then bring in independent specialists, then synthesize. (This is exactly what `/review` does across 4 rounds.)

## 7. Cross-disciplinary review breaks the echo chamber
A chemistry paper reviewed only by chemists inherits the blind spots of chemists. A mathematician doesn't know what's "obvious" in chemistry — so they question the assumption a chemist takes for granted. Run distant disciplines on the *same* object; when two unrelated specialists independently flag the same thing, that's a strong signal. (`/insight`.)

## 8. Honest scope beats an over-reached proof
When formalizing claims into machine-checked theorems, prove the algebraic **core** and explicitly mark the boundary (machine-checked / pen-and-paper / unproven). A sound proof of a physically empty statement is worse than an honest "not formalizable today" — it manufactures false rigor. (`/formalize`.)

## 9. Treat evidence tiers as a hierarchy
Screening models (e.g., ML potentials), equilibrium estimates, and first-principles calculations are *different tiers of evidence*. Don't let a weaker tier silently overrule a stronger one. State which mechanism was actually modeled and which effects were omitted.

## 10. Record immediately
Context is lost at the end of a session. The moment you get a number, make a decision, or find a bug — write it to a file, not to memory. Commands here write intermediate outputs to `tmp/`; final results go where you specify.

---

## The rigor pipeline (how the commands compose)

```
draft → /review          (4-round anti-bias review: is the argument sound?)
      → /groundcheck      (per-claim AI-slop gate: is every fact grounded?)
      → /formalize        (turn the core claims into machine-checked theorems)
      → /insight          (what can't the authors see from inside the field?)
      → /triz             (stuck? systematic invention to break the contradiction)
```

`/review` asks *"is it correct and well-argued?"*; `/groundcheck` asks *"is every load-bearing fact real?"*; `/formalize` asks *"which claims can we prove outright?"*. Use them together — they catch different failures.
