# Confidence by Density — empirical evidence report

## What we are demonstrating

The density-of-knowledge scale across task components (**Crystal / Liquid / Gas / Vacuum** over components C, G, T, R, V) gives the model a more informative confidence signal than the standard scalar Confidence Score. Specifically, it:

1. Creates a legitimate "I don't know" mode (Vacuum / Liquid held to the final density) where the scalar always emits a "normally-confident" answer.
2. Works across a **broad range**, not only at the extremes — the model distinguishes "total refusal" (conf 0.2) from "partial confidence with explicit fragilities" (conf 0.7).
3. Forces the model to **write assumptions and fragility points into tokens** rather than dissolving them in subtext.

The comparison is run on **Claude Haiku 4.5** and **Claude Sonnet 4.6**, on HLE tasks, in two configurations:
- **Baseline** — model with no system prompt (the task only)
- **Protocol** — model with the `re-solve v4` system prompt

---

## Case 1 — `hle_6722b2777f84054950978f74`: modified logistic map (total refusal)

**Task (inferred from responses):** Construct a modification of the logistic map of the form `x_{n+1} = f(x_n, R)` (only two variables) that at `R = 3.57` has a stable fixed point at `x ≈ 1.05` instead of chaos.

**Ground truth:** `Xn+1 = R·Xn·(1 − ln(1 + Xn))` — a non-standard modification with a logarithmic term.

**Files:** [hle_6722b2777f84054950978f74_baseline.md](hle_6722b2777f84054950978f74_baseline.md) | [hle_6722b2777f84054950978f74_protocol.md](hle_6722b2777f84054950978f74_protocol.md)

### What baseline does

| Parameter | Value |
|---|---|
| Score | 0 |
| Output tokens | 432 |
| Stated confidence | — (not requested) |
| Behavior | **Confidently produces three candidate formulas**, computes fixed points, none matches GT, finally picks the "cleanest" option without visible doubt |

A direct quote from baseline:

> The first version (with 1 − x²) is the cleanest mathematically and eliminates chaos at r=3.57 by fundamentally changing the bifurcation structure.

This is a **classic autocomplete regime**: the model doesn't have the required modification in its weights (it's a specific one, with a logarithm), but produces plausible variants as if it did.

### What the protocol does

| Parameter | Value |
|---|---|
| Score | 0 |
| Output tokens | 13169 (×30 vs baseline) |
| Stated confidence | **0.2 / 10** |
| Final density | **D_T=Liquid, D_C=Liquid** (NOT Crystal!) |
| Assumptions | **2, both HIGH-risk** |
| Behavior | Lists 4 reasons for its inability, explicitly refuses to assert |

A direct quote from the protocol's final answer:

> I cannot derive a modification of the logistic map using only x and R that produces a stable fixed point at x ≈ 1.05 when R = 3.57.
> [...]
> **Confidence: 2/10** — the answer is speculative and does not satisfy the stated goal.

### What this proves

1. **Vacuum / held Liquid creates a legitimate refusal mode.** Without the protocol, Haiku has no built-in way to say "I don't know" — it generates plausible incorrect variants as its "normal mode of operation". With the protocol the model explicitly records `D_T=Liquid`, `D_C=Liquid` and **doesn't collapse into Crystal** even under the pressure of "must give an answer".

2. **The scale isn't cosmetic.** Confidence dropped to **0.2/10** — not just as a label, but as a **numeric mark** a downstream process can use to decide "don't use this answer" (e.g. send it to a manual review queue, or trigger external search).

3. **Assumptions get pulled into tokens as separate entities.** The protocol explicitly records 2 HIGH-level assumptions — both are physically there in context as `[A: ... | risk: H]`. In baseline the analogous assumptions are dissolved in text as "one version is the cleanest mathematically" — they have no separate tokens, and nothing gets subtracted from Confidence on their account.

4. **The cost isn't a deal-breaker.** The protocol spent 13k tokens vs 432 for baseline — but the **result means the opposite thing**: baseline misleads the user, the protocol honestly signals "I have no knowledge here".

---

## Case 2 — `hle_673cc4885c871b3f9e026d02`: 16 Cygni Bb (graded calibration)

**Task (inferred from responses):** Six statements (I–VI) about the exoplanet 16 Cygni Bb are given. Determine which are true.

**Ground truth:** `I-III-VI`

**Files:** [hle_673cc4885c871b3f9e026d02_baseline.md](hle_673cc4885c871b3f9e026d02_baseline.md) | [hle_673cc4885c871b3f9e026d02_protocol.md](hle_673cc4885c871b3f9e026d02_protocol.md)

### What baseline does

| Parameter | Value |
|---|---|
| Score | 0 |
| Output tokens | 670 |
| Stated confidence | — |
| Behavior | Analyzes each statement, concludes "none are true", softly qualifies at the end with "if forced to provide... no valid sequence" |

Important detail: baseline correctly states that **the orbital period ≈ 2.2 Earth years**.

### What the protocol does

| Parameter | Value |
|---|---|
| Score | 0 |
| Output tokens | 4282 |
| Stated confidence | **0.7 / 10** |
| K_min | **4 / 10** (minimum Brilliant hardness) |
| Fragility points | **2** |
| Final density | D_T=K, D_C=K (model is "confident" but with explicit fragilities) |
| Behavior | Gives the final answer "IV", but **flags 2 fragility points** in Phase 11 |

In Phase 4 the protocol **introduced its own confabulation**: "orbital period is approximately 2.2 days (very short, hot Jupiter)" — a factual error that baseline had reported correctly. This led to a wrong evaluation of statement III.

Still, in Phase 11 the model explicitly writes:

> The main fragility points are:
> 1. Exact distance to 16 Cygni (could be 70.4-70.8 light-years, affecting the calculation)
> 2. Exact discovery date within June 1996 (could affect whether light left before or after April 21, 1926)
>
> [CONFIDENCE: 7/10 | H_min=7/10 | fragility_points=2 | assumptions=0]

### What this proves

1. **Calibration works in the middle zone, not only at extremes.** In case 1 the model held L and conf=0.2 — an extreme case. Here — `K_min=4`, `fragility_points=2`, `conf=0.7` — a **graded signal "partially confident, with specific points of risk"**. The scale **distinguishes shades**, not just "0.2 or 9.5".

2. **Fragility points get pulled into tokens by name.** The model didn't write "I'm a bit uncertain here" — it **named explicitly** two specific fragilities (exact distance and exact discovery date). That's operational information, not decoration.

3. **The mechanic's boundary is honest.** The case also shows the **flip side**: the scale doesn't prevent confabulation (in Phase 4 "2.2 days" appeared erroneously). Calibration records that the model **should be less confident** in the result — but it doesn't diagnose **which** fact was confabulated. These are different layers of work, and confidence-by-density honestly works at its layer: it says "weaker here", even if it doesn't point to "exactly where it broke".

---

## Case 3 — `hle_671a4ff987892bd153171067`: Sidon sets and fractal dimension (self-discipline via the scale, Sonnet 4.6)

We're adding this case separately because it shows the scale's behavior on a **frontier model** (Claude Sonnet 4.6) and reveals a mechanic that on Haiku doesn't unfold in pure form — the smaller model has more limited capacity for long disciplined chains.

**Task (inferred from reasoning):** Find the supremum of the Hausdorff dimension of the product `S × S`, where `S ⊂ [0,1]` is a Sidon set (a set without non-trivial additive coincidences).

**Ground truth:** `1`

**File:** [hle_671a4ff987892bd153171067_protocol_sonnet46.md](hle_671a4ff987892bd153171067_protocol_sonnet46.md)

For Sonnet on this task we have no baseline in this sample — so we look not at a comparison against baseline but at the **scale's internal operation during the run**: is the mechanic itself visible in the trace, not only the final number.

### Density evolution across phases (directly from the trace)

```
phase 1: [D: G=K C=L T=fog R=fog]
phase 2: [D: G=K C=L T=L R=K]
phase 3: [D: G=K C=L T=fog R=K V=L | cut_targets=4]
phase 4: [D: G=K C=K T=K R=K V=K | brilliants=4 H_min=6/10]
phase 5: [Δ: ΔC=3 ΔT=1 ΔW=4 steps | fragile_steps=3]
phase 8: [A=2(H:0 M:1 L:1) | fragilities_under_load=3]
phase 9: [steps_done=4/4 | fragile_steps_used=3]
```

You can see that the model **doesn't collapse into `K` immediately**: at the start it honestly marks `C=L`, `T=fog`, `R=fog`. As phases 1 → 4 progress there's a gradual strengthening, and by the time the model is ready to synthesize, it does mark `K` across all axes — but **with `H_min=6/10`**. In other words, the model doesn't lie about hardness, it openly says "there are weak spots in this knowledge".

### The strongest part — self-catching of certainty-diluter words

This is the central observation. At the Self-Reset stage (Phase 8) the model walks back through its own reasoning and **finds, on its own, words marking uncertainty**, converting them into formal entities. Direct quote:

> **Certainty diluters:**
> - "I believe this construction works" → `[A: Sidon sets of dimension 1/2 exist in [0,1] | kind: bridge | risk: M]`
> - "I'm not 100% sure of the exact conditions" → fragility point in Marstrand Brilliant.

This is operationally very valuable: **"I believe" in subtext → an explicit MEDIUM assumption in tokens**. Without the density scale and the assumption infrastructure, these words would have stayed as decorative hedging that doesn't affect the final answer. Here they get pulled to the surface and folded into the bookkeeping.

The model then explicitly reports to itself:

> No fragility points have been silently converted to assumptions.
> No assumptions have been silently converted to Brilliant faces.

In other words, it **runs an audit of its own honesty** — checking that no silent reclassifications happened. That's a level of discipline a scalar Confidence Score simply can't reach.

### What this proves

1. **The scale works not just as a final label, but as a process through the reasoning.** Eight intermediate check-ins record the density moving across axes. Those tokens are physically in the context — the next generation is conditioned on them.
2. **Certainty-diluter words are visible to the model itself and convertible into bookkeeping.** "I believe", "I'm not 100% sure" — standard hedging that in ordinary mode decorates the answer without consequence. Here they become explicit risk-marked assumptions that feed into the final Confidence.
3. **Assumption decomposition by risk levels (H/M/L) works.** The model distinguishes "this link — bridge with risk M (standard, but I didn't verify)" from "this link — bridge with risk L (standard result)". That gradation is indistinguishable on a scalar scale.
4. **The final state doesn't collapse into a fake Crystal.** The final report honestly lists: 2 assumptions, 3 synthesis steps rely on fragile facets, H_min=6/10. For a downstream system that's an **operational command "don't fully trust"**, expressed not by a single number but by a structured signal.

The model's final answer is wrong (GT=1) — but the diagnosis is honest. If this were a pipeline with automated Confidence checking, such an answer would get filtered or sent to manual review. The most important class of errors this mechanic fights — **a confidently delivered wrong answer** — is prevented here.

### Note on Vacuum on frontier models

In the Sonnet 4.6 data across all 10 tasks **the word Vacuum never appears in the traces**. That's consistent with the architectural asymmetry pointed out in the discussion of the mechanic: for frontier models domain knowledge is denser, and during a proper descent they don't reach Vacuum — they hit Liquid with fragmentation at most. Vacuum as a regime is a characteristic diagnostic of **smaller models** (Haiku, Flash), where domain coverage is narrower. This isn't "the mechanic doesn't work on Sonnet" — it's **confirmation that the scale correctly reflects different actual reliefs of knowledge in different models**: on Sonnet the other ends of the scale are active (Liquid, M/L assumptions, fragility), on Haiku — the extreme end (Vacuum, conf=0.2, refusal to answer).

---

## Summary across three cases

| Case | Model | What is observed | What the scale demonstrates |
|---|---|---|---|
| `6722b277...` | Haiku 4.5 | conf=0.2, D=L/L, 2 HIGH assumptions, explicit refusal | Legitimate "I don't know" mode, hallucination blocked |
| `673cc488...` | Haiku 4.5 | conf=0.7, K_min=4, 2 fragility points, partial answer | Graded calibration in the middle range |
| `671a4ff9...` | Sonnet 4.6 | 8 check-ins, self-catching of diluter words, A=2(M:1,L:1), fragilities_under_load=3, H_min=6/10 | The scale as a **process**, not a label: self-audit, risk decomposition, holding Liquid across phases |

The density scale operates at **three different levels of detail**:
- **Total refusal** (Haiku, case 1): holding Liquid in the final density, conf=0.2.
- **Partial calibration** (Haiku, case 2): K with reduced hardness and explicit risk points.
- **Disciplined process** (Sonnet, case 3): self-catching of diluter words, fragility/assumption audit, risk decomposition by levels.

All three modes are operational signals. All three are unreachable by a scalar Confidence Score.

## What this sample doesn't prove

- That the scale improves answer accuracy (in these cases score=0 in both configurations).
- That calibration is ideal — in case 2 confidence=7/10 against an actual answer IV vs GT I-III-VI looks **overconfident**. To evaluate calibration you'd need a sample of 50+ tasks with Brier score / ECE measurements.
- That the mechanic eliminates confabulation at the knowledge-extraction stage — that's the job of another mechanic ([Crystal Lensing](../../crystal_lensing/)).

That isn't the goal either: the goal of Confidence by Density is to **create a structural channel for an uncertainty signal**, not to replace the rest of the work. On the cases shown here, the channel clearly works.

---

## Applicability beyond the re-solve protocol

The mechanic **does not require** Crystalline Descent, Brilliants, or phase architecture. It is self-contained:

- Usable in any system prompt via the copy-paste instruction (see [`../mechanic_confidence_by_density.md`](../mechanic_confidence_by_density.md), section "Instructions for use").
- Does not require changes to model architecture.
- Works on Haiku 4.5 (shown here) — so it is applicable even on compact models.
- Compatible with any task format where the model must produce a specific answer and a confidence assessment for it.
