# Confidence by Density — confidence estimation via aggregate state of knowledge

## The problem this mechanic addresses

In tasks that demand a single definite answer (HLE-class benchmarks, MMLU-Pro, GPQA, olympiads, expert diagnostics) **the most dangerous error a model can make is a confidently delivered wrong answer**. Nothing flags such an error: not the user's request "rate your confidence", not a verbalized confidence score, not the internal log-prob distribution.

A standard request to "rate your confidence from 0 to 1" produces an artifact: models almost always answer in a narrow band of 0.6–0.9, and this band has practically no correlation with actual correctness. From the model's perspective "0.7" just means "I see no obstacles, going to solve". That isn't calibration — it's polite decoration.

What makes the problem worse specifically for LLMs:

- **The 0–1 scale was designed for humans and doesn't fit LLM architecture.** Asking a model to rate confidence on a 0–1 scale is roughly like asking Einstein and Darwin combined to rate their confidence in an answer. From the perspective of any LLM — even Haiku — its baseline level of knowledge in any domain is **higher than any living human's**: the model has seen billions of texts where this area gets mentioned. This architectural asymmetry shifts its natural "0.5" somewhere up to 0.7 and above — not out of some self-confident personality, but because it objectively "knows more" than the interlocutor the human scale was calibrated for. For a human, self-criticism is a behavioral skill; for an LLM "low confidence" contradicts its actual distribution of knowledge.
- **One confident component drowns out the rest.** If the model knows even one piece of the task perfectly — say, the solution method — it dominates the aggregate estimate. Complete ignorance of context gets "forgiven" in the average: the result still lands around 0.8–0.9. A scalar scale doesn't distinguish "know the method, don't know the context" from "skimmed superficially everywhere" — not because the model is lazy about it, but because **the scale itself lacks the resolution** to separate those situations.
- **Assumptions get dissolved.** When the model builds a solution on top of 3–4 implicit assumptions, none of them gets accounted for separately. They all enter the final estimate as "generally fine", but each is a potential breaking point.
- **"I don't know" is missing as a mode.** The 0–1 scale has no natural place for a categorical admission "I have no knowledge of this". 0.1 is already an estimate of some kind, not a refusal.

## How the field addresses this today

In research and engineering practice there are several approaches:

- **Temperature scaling / probability calibration** — post-hoc calibration of probability distributions on a holdout set. Works for classification, transfers poorly to free generation.
- **Monte Carlo dropout, ensembles, self-consistency (Wang et al., 2022)** — confidence estimation through variance across multiple runs. Compute-expensive, and doesn't reflect the structure of the uncertainty itself.
- **Verbalized confidence via RLHF** — training the model to state confidence truthfully. Reduces overconfidence, but still collapses to a scalar and is fragile on out-of-distribution tasks.
- **Process Reward Models, step-level confidence** — confidence estimation at each reasoning step. Close to what we need, but requires either an external verifier or specialized training.
- **Retrieval-augmented approaches (RAG)** — sidesteps the problem: if knowledge is external, confidence rests on whether a source is there. Doesn't help where no source exists and the right answer is to refuse.

What these approaches share: they work with the *degree* of "how confident the model is in the answer", but not with the **geometry** of uncertainty — where exactly the line between knowledge and ignorance runs inside the task.

## How I propose to address this

Replace the 0–1 scale with a **qualitative semantic scale of aggregate states of knowledge** and apply it **per task component separately**.

### The four-state scale

- **Crystal** — knowledge you can step on. A formula, definition, theorem, algorithm step that you can reproduce right now, in these very tokens, without hedging. Any further inferences resting on Crystal remain equally solid.
- **Liquid** — knowledge with viscosity. The general statement sounds right, but details flow under load. By the fifth reasoning step divergences begin.
- **Gas** — knowledge is absent or fragmentary. Any statement produced in this state is generation, not recall. Hallucination from the first token.
- **Vacuum** — the model does not even know that such an object or area exists. This is not failure, this is **data**: the absence of a bridge in that direction is itself useful information.

### Decomposition by task components

Confidence is recorded not as a single number, but as a vector over task components:

- **C (Context)** — factual material
- **G (Goal)** — what exactly needs to be produced
- **T (Tool)** — the method / algorithm of solution
- **R (Role)** — the expert lens through which the task is interpreted
- **V (Verification)** — the ability to verify the result

Each component gets its own state independently. This separates "I'm sure about the method, but the context flows" (Gas in C, Crystal in T) from "I know what to do, but not how" (Crystal in G, Vacuum in T) — previously both situations collapsed into "0.7".

### Assumptions as a first-class entity

If at some step the model is forced to fill a gap with its own guess — this is an **assumption** with explicit risk labeling (High / Medium / Low). Assumptions **do not dissolve** into general confidence — they are **algorithmically subtracted** from a base of 1.0:

```
Confidence = 1.0
            − Σ(penalty per HIGH assumption, ~0.1–0.2)
            − Σ(penalty per MEDIUM, ~0.05–0.1)
            − Σ(residual fog in variables, ~0.05)
```

This converts confidence from a "general feeling" into **bookkeeping over an explicit list**.

## Why it works — my observations

The "ground under your feet" metaphor isn't decoration, it's a working image for the model. In an ordinary conversation it's fine to stand on soft soil — your interlocutor will forgive a small slip. In PhD-level tasks there's only one path — **stand on a reinforced concrete foundation**, because on liquid soil a chain of 7–10 logical steps guarantees a cascade of errors that crashes the answer.

I deliberately don't try to express the degree of Crystal in percentages. Crystal isn't "90% confidence", it's a mode where the next reasoning step **doesn't blur** the previous one. It's a categorical property, not a quantitative one.

When the model writes `D_C=Liquid`, `D_T=Crystal`, `D_V=Gas` into its own tokens — it isn't just reporting confidence. It **segments the task** and sees the point of rupture: "I know how to solve, but not how to verify". That point is where you either bring in a resource (search, Crystal Lensing), or honestly mark the gap and drop the final Confidence.

The main observable effect — the model **stops papering over ignorance with a confident tone**. It gets a legitimate exit: "this is Vacuum, I don't know, and I write that out explicitly". Without the aggregate-states scale, the model falls into autocomplete mode and produces plausible hallucination.

### Why a categorical scale, not a numeric 0–1 per variable

A fair question: if decomposing by C/G/T/R/V solves the scalar problem, why not just give the model a 0–1 scale **per axis**? Then we'd have 5 numbers instead of one.

I deliberately don't do this — and the reason is how models actually work with long continuous scales.

**LLMs work poorly with long continuous scales.** The model has no internal sense of what "confidence 0.1" means or how it differs from "0.2" or "0.35". These numbers showed up in training across many contexts, none tied to a specific operational meaning. When we ask the model to output "0.42", we're asking it to produce an estimate where **we ourselves don't know the resolution of the scale** — and neither does the model. The result is noise with an illusion of precision. Splitting into 5 such scales multiplies the noise, it doesn't reduce it.

**But aggregate states of matter the model knows "from birth".** Training data contains a vast number of texts that describe the properties of crystals, liquids, and gases at an intuitively clear level: crystal — structure, bonds are strong and visible; liquid — overall shape is there, details flow; gas — bonds are essentially absent, everything scatters. The model doesn't need to be taught what these states mean. Carrying this metaphor over to the state of knowledge happens **intuitively**: asking yourself "is this formula a crystal or a liquid for me?" is a question the model answers confidently and meaningfully. Asking "is this formula 0.78 or 0.84?" is a question the model has no native way to answer except by generating a plausible number.

In other words: **a categorical scale with a physical metaphor isn't a simplification — it's a switch to a language the model actually understands**. Crystal means "the bonds are strong, I'm ready to lay out clearly any of the connections that hold this fact in place". Gas means "there are essentially no bonds, any next sentence I write in this area will be generation". These are operational definitions the model can apply its intuition to, instead of being forced to invent the scale from scratch.

## Why it works — from an LLM standpoint

- **A categorical token activates a different reasoning subgraph than a number does.** When the model writes the word `Crystal` or `Vacuum` into its own context, subsequent tokens are conditioned on that category. `Vacuum` triggers patterns like "I don't assert", "external source needed", "refuse to synthesize". The number `0.3` triggers nothing of the kind — in training it isn't tied to a refusal mode.
- **Decomposition by C/G/T/R/V gives the model more attention slots for uncertainty.** Without decomposition, all the task's uncertainty competes for a single scalar and loses to the majority. With decomposition each axis gets its own slot in the output tokens and doesn't get crowded out by the others.
- **Writing assumptions into tokens explicitly changes the attention balance.** When an assumption is marked `[A: ... | risk: H]` and is physically there in the context, later steps account for it through attention. If the assumption is dissolved in subtext, it has no tokens for later generation to attend to, and it's effectively forgotten.
- **Algorithmic subtraction from a base of 1.0 is structurally a simpler format than an abstract estimate.** A model adds and subtracts in tokens much more reliably than it integrates a complex "general impression of the solution" into a single number. Bookkeeping gives a stable procedure.
- **The Vacuum category gives the model permission to stop.** RLHF training rewards models for producing answers. "Vacuum" is an explicit, non-shameful "I don't know" built into the working scale, not into a refusal mode. That takes the pressure of autocomplete off.

## Instructions for use (copy-paste into a system prompt)

```
When solving a task, use the following discipline for confidence estimation:

1. Decompose the task into components:
   - C (Context)      — factual conditions of the task
   - G (Goal)         — what exactly needs to be produced
   - T (Tool)         — the method / algorithm of solution
   - R (Role)         — the expert lens through which you look
   - V (Verification) — how to verify the result after obtaining it

2. For each component rate the state on the scale:
   - Crystal — I can reproduce it right now, in these tokens, without hedging
   - Liquid  — general picture is there, details flow on a long chain
   - Gas     — fragmentary knowledge, any statement will be generation
   - Vacuum  — no knowledge of this; don't even know such an object exists

   Honesty rule: you may call something Crystal only if in the next sentence
   you write out the key element (formula, definition, algorithm step).
   The feeling of knowing ≠ knowing.

3. If you must fill a gap with your own guess —
   mark it as an assumption:
   [A: <content> | risk: H/M/L]
   - H (high)    — the assumption concerns a central link of the solution
   - M (medium)  — the assumption concerns an important but not central link
   - L (low)     — the assumption concerns a peripheral / clarifying detail

4. Before the final answer write out the final density across all axes:
   [D: C=K/L/G/V | G=K/L/G/V | T=K/L/G/V | R=K/L/G/V | V=K/L/G/V]

5. Compute final confidence by the rule:
   Confidence = 10
                − 1.0..2.0 per HIGH assumption
                − 0.5..1.0 per MEDIUM assumption
                − 0.5 per component in the final density
                          that remained Liquid or Gas

6. Prohibition: do not attempt to synthesize the final answer if in the
   final density key components (T or the central cluster of C) remained
   in Liquid or Gas. In that case either request clarification or
   explicitly state low confidence and formulate the answer as
   "best hypothesis, not proven".

7. Format the final output as:
   [CONFIDENCE: X/10 | <final density> | assumptions=N(H:a M:b L:c)]
   <clean final answer>
```

## Implementation in re-solve protocols

| Version | Where implemented | What was added |
|---|---|---|
| **v2** | Section "Density metaphor", Phase 2 (Density_1), Phase 4 (Density_2), Phase 7 (Density_3) | Base scale Crystal/Liquid/Gas/Vacuum. Decomposition over T, C, V (verification — a separate axis). Three generations of variables `_1/_2/_3` with re-evaluation of density. |
| **v3.1** | Section "Architecture of Self-Criticism", `[D]` as a vector variable | Role `[R]` becomes the fourth density axis. Assumptions are mentioned, but without formalization. |
| **v3.6** | `[D]` and `[CF]` in Operating Memory, SKILL: Density Assessment, SKILL: Confidence Calculation | Dynamic variables `[D]`, `[A]`, `[CF]` are formalized. Assumption subtraction algorithm with concrete penalties (−1..−2 for HIGH, −0.5..−1 for MEDIUM). Assumptions become a first-class entity with explicit risk labeling. |
| **v4.1** | Section "Physics of the Environment", mandatory `[D: ...]` check-ins after each phase | Gas and Liquid are redefined as **diagnostic signals of fog** (not working states). At the level of a single step the scale becomes binary: Crystal or Vacuum. Brilliant hardness `[H: X/10]` is introduced as a quantitative measure of how cleanly a specific piece of knowledge has been cut. Assumptions are typed (bridge/patch/replacement). |

## Empirical evidence

See [`benchmarks/report.md`](benchmarks/report.md) — analysis of cases from the HLE benchmark on the Claude Haiku 4.5 and Claude Sonnet 4.6 models:

- `hle_6722b2777f84054950978f74` — modified logistic map: without the protocol the model confidently produces three incorrect formulas; with the protocol it keeps Liquid in the final density, declares 2 HIGH assumptions, and explicitly lowers confidence to 0.2.
- `hle_673cc4885c871b3f9e026d02` — task with partially known material: the model itself sets K_min=4/10 and 2 fragility points, ending with conf=0.7. Calibration is non-trivial even on a failing case.
- `hle_671a4ff987892bd153171067` — fractal dimension of Sidon products (Sonnet 4.6): the scale visibly operates **as a process** — 8 intermediate check-ins, self-catching of certainty-diluter words ("I believe" → an explicit MEDIUM assumption), audit of the fragility/assumption split, risk decomposition by H/M/L levels.

---

## Related research

- **["LLM Confidence Evaluation Measures in Zero-Shot CSS Classification"](https://arxiv.org/abs/2410.13047)** (arxiv 2410.13047, 2024) — Tests categorical confidence labels (none / low / medium / high / absolute) against the standard numeric 0–1 format. Finds ordinal labels yield worse Expected Calibration Error than the float format — switching to a categorical format alone does not fix LLM confidence calibration.

  > **Author's note.** Ordinal labels *low / medium / high* offer no real advantage over numbers like 0.3 / 0.5 / 0.8: neither comes with an algorithm telling the model *how* to arrive at the assessment — the label stays a feeling collapsed into a word. The Crystal / Liquid / Gas / Vacuum scale described here works for a different reason: its labels carry **concrete physical meaning**, not abstract confidence levels. The model doesn't need to be taught what "liquid" means — it already knows bonds are present but weak, shape holds globally but details flow under load. Categorical form only works when the categories carry **concrete meaning** the model already understands intuitively; abstract categories produce the same noise as numbers.
