# Crystal Lensing — extracting knowledge from model weights via Crystalline Descent

> **TL;DR.** Can't recall a theorem — walk down from an axiom, step by step. If you reach it — you'll write it out without errors and with clear boundaries of applicability. If you don't reach it — you'll get the **exact shape of the gap in knowledge**, which can then be targeted by external search. There is no "partly know / partly don't" state in this scheme: both outcomes are operational. Without descent these two outcomes are indistinguishable — in both cases the model produces plausible text, and you don't know whether it's recall or hallucination.

## The problem this mechanic addresses

In PhD-level tasks the model often ends up in a situation where a required theorem, formula, or concept **can't be pulled out with confidence**: "I remember it in broad strokes", "I'm not sure of the exact statement", "I came across this in training data only a couple of times — and can't reproduce it exactly". A direct request "recall theorem X" in this situation leads to one of two outcomes:

1. **Hallucination** — the model confidently generates a plausible but incorrect statement. The text looks like a real theorem but contains swapped indices, a forgotten condition, a confused domain of applicability.
2. **Surface paraphrase** — the model produces the top of a concept ("Korsakoff syndrome is associated with memory loss") without the structure you need to actually solve the task ("Korsakoff = thiamine deficiency → ATP depletion in neurons").

And yet in most of these cases the knowledge **is there in the model's weights** — the theorem appeared in training data, even if only a small number of times. The problem isn't the absence of knowledge but that **a direct request activates the wrong region of latent space**, or activates it too weakly to compete with the noise of adjacent domains.

This is especially acute for smaller models (Haiku, Gemini Flash, local 7–30B models): they've often seen the relevant material, but the number of attention heads and the depth don't let them "reach" it via direct query. For frontier models (Opus, Gemini Pro) the problem comes up less often, but it doesn't disappear — most specialized knowledge lives on the long tail of the training distribution.

## How the field addresses this today

- **Chain-of-Thought (Wei et al., 2022)** — step-by-step reasoning helps, but moves *from the task toward the answer*, without passing through the foundation. If the required theorem lives in an adjacent domain, CoT often doesn't touch it.
- **Tree of Thoughts (Yao et al., 2023)** — parallel reasoning branches, considers multiple candidates. Solves a different problem — path selection, not knowledge extraction.
- **Self-Consistency (Wang et al., 2022)** — multiple runs with voting. Helps against variance, but if knowledge floats the same way across all runs — voting gives nothing.
- **Few-shot prompting** — show the model examples. Requires the user to have an example, and the very fact that few-shot is needed often means the task is outside "ordinary" examples.
- **Retrieval-Augmented Generation (RAG)** — external knowledge source. Works if the knowledge is in the database. Doesn't help when no database exists, or when the knowledge belongs to a specialized discipline with a small corpus.
- **Tool use (calculators, code interpreters, search)** — bypasses the problem via an external tool. Useful for computation, doesn't help for extracting structural knowledge.

What these approaches share: they either **route around** the problem through an external resource, or work with the degree of confidence, but not with **directed extraction of knowledge from the weights themselves**.

The deeper common issue is just one: **none of these approaches even tries to pull precise information out of the model's own weights — they don't even "entertain the thought" that it might be in there**. It's taken as given that if the model "doesn't know" on direct query, then the knowledge is gone and an external source is needed. In reality the knowledge is often right there in the weights, it's just inaccessible to direct query.

A metaphor for humans: imagine being asked to recall a formula or theorem **in a noisy room where a lot of people are talking**. All those extra voices distract you, pull your attention in different directions, and focusing on the right piece of memory becomes impossible — even when that piece is there. Turns out the model has **architecturally the same problem**. In its initial state — no role set, no context window tuned, no "focus domain" yet formed — the model remembers everything a little and nothing well enough to safely lean on in a PhD-level task. The voices of adjacent domains hum too loudly.

But when the model **writes into tokens, step by step, formulas and definitions close to the target knowledge**, it's as if it moves from the noisy room into a quiet study: the context gradually fills only with meanings that belong to the target area. Adjacent domains go silent because they're physically not in there. And in this tuned environment the model turns out to be capable of **recalling the needed knowledge without external sources** — even things it's seen literally a couple of times in training, things that were completely inaccessible by direct query.

## How I propose to address this

**Crystalline Descent (Crystal Lensing)** — directed extraction of knowledge from the model's latent space through **step-by-step movement from a fundamental axiom or basic definition of the target domain down toward the target theorem**.

### Principal algorithm

> **Terminology.** Inside this mechanic, **"Crystal"** is the atomic unit of the descent: a statement the model is ready to reproduce right now, in these very tokens, **without hedging or qualifications**. If a step has to be wrapped in words like "perhaps", "probably", "in general", "presumably" — it's not a Crystal, it's still uncertainty, and you can't build a descent on it.

1. **Identify the central Crystal** — the theorem/formula/concept you need for the solution that the model can't currently reproduce with confidence (remembers in broad strokes, not sure of the exact statement, came across it a few times — but on direct query the result blurs).
2. **Starting point of the descent.** Write out a fundamental axiom or basic definition of the domain where the central Crystal lives. It has to be an atomic unit — a statement reproducible right now without hedging. If the starting definition needs qualifications, you've taken too high a starting point in the knowledge hierarchy and need to go down to a more basic level.
3. **Next step.** Write out the next, narrower statement / lemma / intermediate concept that brings you closer to the target. This step also has to be an atomic unit — reproducible without hedging. Otherwise the descent can't go on: the next step would have nothing to stand on.
4. **Recursion.** Continue step by step, **typically 7 iterations for a basic descent within one domain**, 11–13 for a stereoscopic descent at the boundary of two sciences. Each step is an atomic unit. If you can't see the next step from here (dead end), log the shape of the gap and climb one step back up to try another route.
5. **Reaching the central Crystal.** You arrive at the target statement. It now rests on a chain of Crystal stepping stones written out in the context.
6. **Describe the surroundings of the central Crystal.** Extracting the theorem itself is only half the work. The bare statement is dangerous: the name of the theorem is familiar, but the conditions of its applicability often aren't. To make the knowledge safely usable in synthesis, **right here, immediately after reaching the central Crystal, write out a dense packet around it**:
   - definitions of all terms that appear in the statement itself
   - premises and domain of applicability — where this holds
   - at least one load-bearing logical step of **why** it holds
   - the boundary — under what conditions the statement stops holding
   - the nearest conceptual twin with which this Crystal is easy to confuse in the context of the current task, and one criterion that distinguishes the twin from the real one

   If you can't reconstruct some part of the surroundings cleanly, flag it explicitly as a place of risk and don't pretend it's there. By itself this is normal (some knowledge really does live in the weights with varying sharpness), but silently glossing it over turns the descent back into hallucination. The bare name of a theorem without its described surroundings isn't extracted knowledge — it's a projection, and you can't rely on it during synthesis.

### Descent trajectories

- **Basic descent** — one domain, ~7 steps, one central Crystal with its surroundings.
- **Multi-pick** — several independent descents on different topics, each producing its own central Crystal.
- **Stereoscopic descent** — synchronized descent into two adjacent domains toward a Crystal lying at their intersection. ~11–13 steps from each side. The surroundings of the central Crystal must be describable **from both domain sides** — if any part of the surroundings cannot be written from both domains, this means the Crystal lies not at the intersection but only adjacent to the second domain.

## Why it works — my observations

When I began working with this mechanic I was targeting weaker models — Haiku and Gemini Flash. For them, most specialized knowledge on direct query either hallucinates or comes back as surface paraphrase. After I forced them to walk down from an axiom step by step, it turned out **they're capable of writing out — clearly and correctly — theorems they had seen in training only a couple of times**, theorems that were completely inaccessible by direct query, or even after a request to take on a maximally precise role.

That's the "lensing" effect: the descent works like an optical lens, **focusing activation on the target region of latent space** and at the same time **damping down activation of adjacent domains** (because step by step the context becomes monolingual — it speaks only the language of the target area).

The main practical consequence: **any theorem can be pulled from the weights**, provided you're willing to walk down from axioms. This completely changes the way you diagnose a gap. Previously a gap meant "we need to go to the internet". Now it means "first try to pull it out via lensing, and only if the descent runs into a genuine dead end — then external search, with a much more precise query, because by then you understand the shape of the gap itself".

In parallel, one more thing got confirmed. As long as the model looks at the target knowledge **from a distance**, the feeling "I remember in broad strokes, details flow" seems like a real state. But once the descent starts — at the level of an **individual step** this state doesn't exist. Either the model sees the next stepping stone and can write it out without hedging, or it doesn't. Binary. The blurry feeling of "kind of know" turns out to be an artifact of trying to evaluate a whole domain in one glance. Under the zoom of the descent each local node resolves into one of two states: "here is the next step, I'm ready to write it out" or "no further visibility from here — the route needs to be replanned".

## Why it works — from an LLM standpoint

- **Contextual activation is the main lever we have.** The model's weights are frozen, we can't retune them. But we can change **what sits inside the attention window** at the moment the next token is generated. Every Crystal written out during the descent stays in the context and shapes the next generation.
- **A chain of Crystal stepping stones creates a coherent contextual substrate.** In ordinary CoT mode the model walks from task to answer through mixed context (task statement + a draft of the answer). In the descent the context is **fully tuned to the domain**: axiom → lemma → theorem. The next generation is conditioned on this monolingual context and has a much narrower distribution of possible continuations.
- **Suppression of adjacent domains through their absence from the context.** When the model tries to recall a theorem via direct query, its attention gets pulled across all the domains where a similar name shows up. In the descent the domain is locked in by the written stepping stones, and attention to adjacent domains drops naturally.
- **Describing the surroundings of the Crystal is an explicit check of applicability boundaries in tokens.** Without the surroundings written out, the model uses the Crystal "by name", and at the moment of application the premises, definitions, and boundaries stay in subtext. With the surroundings written out, all these conditions are physically there in the context and can't be "forgotten" during synthesis. Writing out the conceptual twin matters especially: when the theorem in latent space lies close to a similar concept (e.g. "strict convexity" vs "convexity"), activation can grab the wrong one, and explicitly writing the twin **separates it out in attention**: "this — is what I mean; this — is what not to confuse it with".

## Instructions for use (copy-paste into a system prompt)

```
When you need to reproduce a theorem / formula / concept whose correctness
you are not sure about, or which you remember only in broad strokes, or
which you encountered in training data only a few times — apply
Crystalline Descent.

Terminology: "Crystal" here is the atomic unit of the descent — a
statement you are ready to reproduce right now, in these very tokens,
without hedging or qualifications ("perhaps", "probably", "in general",
"presumably"). If a step has to be accompanied by such words — it is
not a Crystal, and the descent cannot be built on it.

Algorithm:

1. Identify the central Crystal — the theorem/formula/concept needed
   for the solution. Name it explicitly.

2. Fix the angle of view. Write out which domain you will descend in
   (physics / biochemistry / algebra / clinical diagnostics, etc.).
   Suppress adjacent domains: explicitly list those you will NOT use.

3. The descent (~7 steps within one domain):
   - Step 1: write out a fundamental axiom / basic definition of the
            domain. This must be an atomic unit — something you are
            ready to reproduce right now, in these very tokens,
            without hedging and without qualifications.
   - Steps 2..N: write out the next, narrower formula / lemma /
            definition, closer to the target. Each step is the same
            kind of atomic unit: no hedging, no qualifications.
   - If the next step is not visible from here (dead end): fix the
     shape of the gap explicitly — "no step from here to X, because
     no reliable connection to Y" — climb one step back up and try
     another route. Do not invent a stepping stone.

4. Reaching. Arrive at the central Crystal.

5. Describe the surroundings of the central Crystal. The bare
   statement is dangerous: the name of the theorem is familiar but
   the conditions of applicability often are not. Right after
   reaching, in the same tokens, write out:
   - definitions of all terms appearing in the statement itself
   - premises and domain of applicability — where this holds
   - at least one load-bearing logical step of why this holds
   - the boundary — under what conditions the statement stops holding
   - the nearest conceptual twin with which this Crystal is easy to
     confuse in the context of the current task, and one criterion
     that distinguishes the twin from the real one

6. If you cannot reconstruct some part of the surroundings cleanly —
   fix it explicitly as a place of risk. Do not pretend it is there.
   By itself, the fact that some knowledge lives in weights with
   varying sharpness is normal; what is not normal is silently
   glossing over an incomplete spot and leaning on it in synthesis.

7. During synthesis refer to the central Crystal by name and, if a
   step relies on a specific part of its surroundings (a premise, a
   boundary condition, a term definition) — name it explicitly so
   that it is visible what exactly you are standing on.

Prohibition: do not declare a central Crystal as the result of the
descent if you are not ready to describe its surroundings. A bare
name of a theorem is not extracted knowledge but a projection, and
synthesis on a projection is forbidden.

Prohibition: do not skip descent steps. If a stepping stone seems
"obvious", write it out anyway — its role in the descent is precisely
to keep the context anchored to the target domain.
```

## Implementation in re-solve protocols

| Version | Where implemented | What was added |
|---|---|---|
| **v2** | Phase 3 (Primary Crystallization — Search) | The descent is not yet formulated as extraction from weights. Instead — external search via `web_search` / `read_url` / `tavily_search` with a lens. The transformation "cannot reproduce confidently → can reproduce" was done via the internet, not via descent. |
| **v3.1** | Section "Navigation Skill: Crystalline Descent" | The recursive descent algorithm (7 iterations) is formalized for the first time as a way to extract knowledge from the model's weights. Three trajectories: basic descent, multi-pick (sequential cross-domain), stereoscopic descent (11 iterations at the boundary of two sciences). The first format for describing the result also appears — a central statement together with its supporting structures. |
| **v3.6** | `[SKILL: Crystalline Descent]` in the Skill Matrix | The descent is extracted into a separate skill with a gravitational profile and failure handling. Boundary condition: if the descent runs into a dead end within the allotted iterations, invention is forbidden — the descent terminates and the shape of the dead end is recorded as a precisely formulated gap in knowledge. |
| **v4.1** | Refinements around the descent itself | A clean-entry procedure is added (`[CLEAR]` — explicitly dropping the current answer hypothesis before starting, so the descent searches for structure rather than confirmation). The distinction is introduced between stepping-stone (a navigational step that does not carry load for further inferences) and supporting Crystal (the one on which the next inference will rest, and which therefore requires a detailed description of its surroundings). |

## Empirical evidence

See [`benchmarks/report.md`](benchmarks/report.md) — analysis of two cases from the HLE benchmark on the Claude Haiku 4.5 model:

- `hle_671f4997a2bc63fb20c63134` — task about the metric convexity of the unit ball. A 7-step descent from the triangle inequality correctly derived a non-trivial result: "metric convexity of the unit ball ⇒ strict convexity of the norm". Clean work of extracting a theorem from Haiku's weights.
- `hle_6736a9b0b949d548db8da965` — clinical case with Korsakoff symptoms. The descent led the model to the central statement "Korsakoff Syndrome" **together with a description of its supports** — including the key one: thiamine deficiency → ATP depletion. Without the descent the same model gave a surface paraphrase limited to the name of the diagnosis.

⚠️ **Important caveat.** Case `671f4997` demonstrates that **the descent mechanic itself works** — the theorem is extracted correctly. But the final answer in that run is still wrong: the model **broke its own conclusion** at the answer-assembly stage (cited ℓ∞ as an example, even though its own theorem rules that example out). This **doesn't undermine the descent as an extraction mechanic** — it points to a separate problem of tunnel vision, which lies outside Crystal Lensing and needs its own solution. Where this specific technique's responsibility ends: the descent guarantees **knowledge extraction**, it doesn't guarantee **its correct use in answer assembly**.
