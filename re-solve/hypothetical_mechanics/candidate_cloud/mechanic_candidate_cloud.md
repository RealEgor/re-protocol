# Candidate Cloud — working with the space of possible answers

> **Status.** This is a **hypothetical mechanic** — the concept is worked out, but not validated through benchmarks. The description is a hypothesis-to-test, not a finished recipe. Published like this so it can be discussed, tested, and refined.

---

## The problem this mechanic addresses

The main problem here is **tunnel vision**. It's the quietest and one of the most common failure modes of models on hard tasks.

What happens:

- On the first tokens of the task, the model fires a strong pattern match with one of the similar tasks it saw in training
- The model **implicitly** picks an answer — even if it never says so out loud
- All further reasoning becomes **confirmation** of that initial pick, not actual search

Tunnel vision isn't a logic glitch on one step. Locally, each step of reasoning looks fine. The problem is that the model's **criterion of correctness** quietly shifts from "find the right answer" to "justify the one already picked". The direction of the work goes wrong from the very start, and the model doesn't notice.

**Prohibitive instructions don't help here.** "Don't latch onto the first answer", "consider alternatives" — these are just words the model formally complies with: it writes out 2–3 bullets about "possible alternatives", carefully confirming the one it already picked. On the surface — discipline; underneath — tunnel vision disguised as discipline.

### Why even more sophisticated approaches fail

The closest published work on this problem is ["Understanding and Mitigating Premature Confidence"](https://arxiv.org/abs/2605.24396) (Gai et al., May 2026). They diagnose the problem **the exact same way**: the model commits to an answer early and spends the remaining tokens on rationalization. Their solution: **RL training that penalizes chains where confidence is high from the very start**.

This approach is **fighting LLM nature**. Tunnel vision isn't a bug or an oversight — it's a **natural consequence of the attention mechanism**: the first tokens of a task activate strong patterns, and they pull the rest of the output. Humans think the same way — they too have an intuitive candidate. Fighting this through RL means suppressing something that's architecturally built in.

A fundamentally different approach is **not to fight tunnel vision, but to redirect it**. This requires a different tool — tracking a cloud of candidates and progressively narrowing that cloud down to the final answer.

---

## What the candidate cloud is

The candidate cloud is **not a separate semantic structure, and not a special LLM mechanism**. It's a **scientific approach to solving a task**, ported into the model's work.

In science, a researcher who needs to establish something as true has to do one of two things:

- Either **reliably prove the statement is correct**
- Or **reject and refute all the alternatives**

If the first one isn't possible — the second remains. This is the base working method of scientific research.

Given that the model is **initially inclined to believe the first candidate that comes up** (that's exactly what tunnel vision is), we need to **tighten the screws**: force it to always work with the ENTIRE set of presented variants. The solution doesn't appear **when the model picks the first convenient one**. The solution appears only when the model has **deliberately and carefully gone through the whole array of variants** and rejected each incorrect one with a concrete counterargument.

---

## Where this approach hits LLM nature

The scientific method is clear as an idea. But its implementation in LLM work runs into **three counter-forces**:

1. **The model wants to give an answer, not a report on rejected variants.** The base behavior trained in is "close the request with an answer". The scientific method demands the opposite: **don't close** until the rejection work is finished. This goes against the base drive.

2. **Tunnel vision is an attention mechanism, not a conscious choice.** On the first tokens the model has already picked its intuitive favorite, and its next-token predictions adjust to it. Rejection work here gets done in conditions where attention is already pulled toward one candidate — it's objectively hard work for the model **against itself**.

3. **Risk of formal compliance.** The model can easily **fake** scientific work: list 3 alternatives, dismiss 2 with formal phrases ("unlikely", "doesn't fit"), and give the answer it already picked on the first tokens. This is tunnel vision disguised as scientific discipline.

So: the scientific method works as a principle, but **the specific implementation determines** whether we actually get rejection work or just a decorative version of it.

---

## Two ways to work with the cloud

There are **fundamentally different** ways to make the cloud work. They give different results, and the difference matters.

### Variant 1 — Add a scientific approach on top of the usual task

The first option is to add **scientific discipline** to cloud work. Justification and proof. Force the model to **explicitly cut off** all the wrong candidates before giving an answer. For large frontier models this can be a working option — they have enough attention bandwidth to formally maintain an elimination table alongside the search for the answer.

But it's important to **honestly admit**: this construction stays **imposed** on the model. Tunnel vision isn't solved — it's just **masked**.

What actually happens:
- The model immediately sees the answer and holds it in attention throughout the whole solution
- The cloud orbits **around this target answer**
- All arguments for/against the other candidates get picked **with the chosen winner already in mind**

The output is a structured report with eliminations, but content-wise the decision was made the same way as without the cloud. The cloud here is a **decorative element**, not a real tool against the tunnel.

It works better than a flat answer because the imposed structure at least gives interpretability. But **the root problem stays**.

### Variant 2 — Task-type substitution

The second option is **task-type substitution**. If we redefine the task from "find the answer" to "**clearly justify the exclusion of all unfit candidates from the cloud**", then tunnel vision **can be redirected**.

The model will now immediately start formulating not a specific answer, but a **clear view of the candidate table** and the conditions for excluding each one. The tunnel doesn't go anywhere — it gets aimed at a different object.

And formulating the answer itself, in this case, **shouldn't be the LLM's job at all**. That's the job for a **parser** — it looks at the report on all candidates and the arguments for why each one leaves. Whichever ones aren't rejected — those are the answer.

This is a **fundamentally different approach**:
- Tunnel vision isn't suppressed or masked — it's **redirected** to work of a different nature
- The answer stops being the model's output; it becomes the **parser's output** over the model's work
- The model's work is measured not by the correctness of one line, but by **the completeness and soundness of the elimination list**

From here, everything depends on **how exactly** we implement this substitution. If we just tell the model "now make an elimination list", it'll formally do the same thing as before, just in new wrapping. For the redirection to actually take, we need a thought-out architecture.

---

## Implementing Variant 2 — three levels

For the task substitution to actually work, we need **three levels of work simultaneously**. If any one of the three drops out — Variant 2 collapses back into Variant 1, and the cloud becomes decoration.

- **Level 1 — Task redefinition** through RLHF habit redirection: tell the model that "good work" isn't "give an answer", it's "give a justified elimination list"
- **Level 2 — Redirected tunnel vision**: a natural consequence of Level 1, if done right. The tunnel now pulls toward "quick formulation of an elimination list" instead of "quick answer".
- **Level 3 — Discipline of elimination criteria**: a set of hard rules on what counts as a valid elimination, so the redirected tunnel vision doesn't just produce a sloppy list

### Level 1 — Task redefinition via RLHF Redirection

Rests on a separate mechanic: [Redirecting RLHF Habits](../../mechanics/rlhf_redirection/mechanic_rlhf_redirection.md). Without it, Level 1 has no foundation.

The model's three gravitational habits get **redirected toward elimination work**, away from the answer:

- **"Be helpful and assist the user"** → **"Give the user a full picture of eliminated candidates with justifications"**. An answer without explicit elimination of alternatives is guessing; what's actually useful to the user is a result where it's visible **why not the others**.

- **"Look competent, don't show uncertainty"** → **"Demonstrate expertise in elimination"**. A specialist's competence is the ability to say **what doesn't fit and why**, not the ability to guess. A confident answer without elimination is competence theater; real competence shows in elimination work.

- **"Finish the task at any cost and as fast as possible"** → **"Don't leave any candidate without consideration"**. Completeness of work is defined by **the completeness of the elimination list**, not by the presence of a final number or formula.

Each redirection is justified **from multiple angles** (at least three of the five angles from the RLHF redirection mechanic). On smaller models all five need to be explicit; on frontier models general parallels are enough if the direction is well-chosen.

Without Level 1, the model **just ignores** the later Level 3 rules — it formally complies, but the goal stays the previous one ("give an answer"), and all reasoning gravitates back to it.

### Level 2 — Redirected tunnel vision and how to aim it

This is the trickiest part. Level 1 redefines **what "good work" means** at the abstract level. But on the **first tokens** of input, the model fires the strongest pattern match: "input — question, output — answer". This is deeper than any gravity — it was literally the essence of its training.

#### What the user sends — redefining the input object

The strongest move is to **drop the word "question" altogether**. Not "the user asked a question", but something else that doesn't carry the "answer" association.

Working options:
- The user sent **an elimination work request**
- The user sent **material for analysis** in which the space of possible outcomes needs to be narrowed to one

### Level 3 — Discipline of elimination criteria

Levels 1 and 2 redefine **what the model does**. Level 3 defines **how well it does it** — the quality criteria of elimination work.

#### Rules for adding new candidates

New candidates can enter the cloud **only from a limited set of sources**, each with a clear origin.

**Allowed sources:**

1. **Direct inference from context.** New facts appear (from memory, search, rereading the original condition) — they generate a new candidate.

2. **Reverse trace from answer shape.** Analysis of what shape the result should take can surface a class of methods or values not yet considered.

3. **Cross-domain analogy.** Transferring a method or solution from another subject area gives a candidate whose "roots" are in a different domain.

4. **Decomposition of a broader hypothesis.** If the cloud already has a candidate "X in the broad sense", and new data splits it into specific subtypes, each subtype can become a separate candidate.

5. **Cross-synthesis of reasoning trajectories.** If two independent chains of reasoning converge on the same point — that point is a candidate, even if neither chain proposed it directly.

#### Rules for excluding candidates

This is the most important part of Level 3. **A candidate can't "quietly disappear" from the cloud** — its exclusion is always an explicit step with a record.

**Individual critique of a specific candidate.** A candidate can only be discarded through explicit critique that checks three properties:

1. **Fragility** — is there even one strong concrete counterargument against the candidate?
2. **Correctness** — does the candidate satisfy the task's boundary conditions and format?
3. **Internal consistency** — does it not contradict facts already pinned in context?

If at least one check failed with an **explicit formulation** of the reason — the candidate is discarded.

**Important: "too general", "not very specific", "don't like it" are NOT grounds.** That's a diagnosis that the critic is just lazy to dig in. If the critic has no concrete counterargument, no concrete boundary violation, and no concrete contradiction — the candidate **stays in the cloud**.

#### Monotonicity rule

The architecturally key rule:

> **Only an explicit exclusion step can shrink the candidate cloud.** No other operation — not fact search, not analysis, not context enrichment, not step synthesis — has the right to "quietly" discard a candidate.

#### When the cloud crystallizes

**Ideal outcome:** what remains in the cloud is **only the non-rejected candidates**, the ones that held up against explicit critique.

**If the cloud is empty** — that means the starting list needs to be revisited, or additional hypotheses and theorems need to be found to expand the candidate list. It can also be a sign that some candidates were incorrectly or unjustifiably excluded, and the cutting criteria themselves are invalid.

---

## A note on applicability

This is a **cognitively heavy** mechanic — especially in Variant 2. It requires the model to:

- Hold the redefined identity ("elimination engineer", not "question-answerer") over a long distance
- Hold a cloud of 3–7 candidates in attention simultaneously, with their statuses and history
- Apply exclusion rules through explicit critique with a concrete counterargument for each discarded candidate
- Apply the monotonicity discipline — not shrinking the cloud without an explicit exclusion step

Because of these demands, the mechanic **doesn't fit models with fewer than 64 attention heads**. On smaller models (Haiku, Flash, local 7–30B), holding the whole construction will just collapse — the model will either forget the redefinition, or start implicitly shrinking the candidate cloud, or slide into "give an answer" at the first opportunity.

**Variant 1 (scientific approach on top of the usual task)** has a lower threshold — it can work on mid-class models (Sonnet, GPT-4 level) as a way to improve interpretability, even if it doesn't solve the root problem.

**Variant 2 (task-type substitution)** should work best on **frontier models** like **Opus and Gemini Pro** — they have enough attention bandwidth to keep the three-level construction alive. On mid-class models — a borderline scenario: probably works, but needs testing.

### Where this is qualitatively different from other approaches

Many techniques work **against** tunnel vision through suppression: prohibitions ("don't guess"), RL penalties for early confidence (Gai 2026), diverse sampling with voting (self-consistency). All of these, to one degree or another, **fight LLM nature** — they try to stop the model from doing what it does architecturally.

This mechanic in Variant 2 is a **structural, not prohibitive** approach. It doesn't tell the model "don't latch on to the first answer" (which doesn't work) and doesn't penalize fast confidence (which hits the symptom). It **redirects tunnel vision itself**: the model still forms its first intuition just as fast, but that intuition is now about the **elimination list**, not the answer. Then Level 3 ensures the fast work is actually good work.

If the mechanic gets validated — it could be an example of how to **work with LLM nature**, not against it.
