# re!solve — a reasoning protocol for PhD-level tasks

Development history, key findings, and interim conclusions.

[English version](README.md) | [Русская версия](README_ru.md)

---

## The original task

I wanted to take the [re!think](../re-think/) protocol and run it on a serious benchmark. The target chosen was **HLE (Humanity's Last Exam)** — currently one of the most resistant benchmarks for LLMs.

That is why the first runs were done on `re!think`, and why the `re-solve/` folder has no `v1` protocol — the count starts with `v2`, the moment it became clear that HLE needs its own protocol.

## Why `re!think` did not fit

After trying `re!think` on HLE tasks, I realized two things:

1. **The Expansion and Bypass modes are not needed.** Every HLE task is a task of finding one specific answer. Multi-mode operation only gets in the way.
2. **`HardStop` had to go.** `re!think` has a function that interrupts the response and asks the user to step in. For a benchmark run that's a non-starter — here you have to work **"in a single beat"**, building the reasoning algorithm directly inside the context window and leaning only on the strengths and weaknesses of the Attention mechanism.

That's how the exploration began, driven by one inner conviction: the claim **"Attention is all you need"** is now ready to be thought through more deeply. If in 2017 the breakthrough was creating the Attention mechanism itself, today we should be thinking not only about how to implement it, but also about **how to write reasoning algorithms designed for it**.

---

## Key problems I entered `v2` with

### 1. The model's excessive overconfidence

A standard Confidence Score doesn't work: models tend to rate it "around 0.7 in general", and for them that means no obstacles — they can go ahead and solve.

To fight this:

- **Localizing uncertainty through variable extraction** (inherited from `re!think`): `C` — context, `G` — goal, `T` — tool. This let me separate justified confidence in the tool from false confidence in the context.
- **A new confidence scale — the aggregate state of knowledge.** The metaphor: knowledge is "the ground under your feet" while you reason. In an ordinary conversation it's fine to stand on soft soil. In PhD-level tasks there's only one path — a reinforced concrete foundation (**Crystal**). I deliberately didn't try to express the degree of Crystal in numbers; instead I explained to the model what reinforced-concrete confidence actually is (the kind where every next inference stays just as crystal-solid) and why **you can't synthesize an answer on liquid, let alone gaseous, knowledge** — the cascade of errors is guaranteed to crash the answer at those precision requirements.

> This mechanic has been extracted into its own folder [`mechanics/confidence_by_density/`](mechanics/confidence_by_density/).
>
> - **Mechanic description:** [`mechanic_confidence_by_density.md`](mechanics/confidence_by_density/mechanic_confidence_by_density.md) (en) · [`mechanic_confidence_by_density_ru.md`](mechanics/confidence_by_density/mechanic_confidence_by_density_ru.md) (ru)
> - **Benchmarks** — the same HLE tasks were run with and without the protocol, showing the essence and operability of the mechanic: [`benchmarks/`](mechanics/confidence_by_density/benchmarks/)
> - **Summary report** on the benchmarks: [`report.md`](mechanics/confidence_by_density/benchmarks/report.md) (en) · [`report_ru.md`](mechanics/confidence_by_density/benchmarks/report_ru.md) (ru)

There is related research on replacing numeric confidence with categorical labels: [arxiv 2410.13047](https://arxiv.org/abs/2410.13047) tests labels like *low / medium / high / absolute* against the standard 0–1 float. But this approach is fundamentally different from what I describe here. Ordinal labels like *low / medium / high* offer no real advantage over numbers like 0.3 / 0.5 / 0.8: in neither case is there an algorithm that tells the model *how* to arrive at the assessment — the label is still just a feeling collapsed into a word. What makes the Crystal / Liquid / Gas / Vacuum scale work is something different: these are terms the model understands at an intuitive level because physical states of matter are densely represented in training data with precise, consistent semantics. The model does not need to be taught what "liquid" means — it knows that bonds are present but weak, that shape is maintained globally but details flow under load. Carrying this over to the state of knowledge is an intuitive step, not a mapping that requires calibration. The choice of this particular analogy was not arbitrary: working with models I repeatedly observed them reaching for matter-state metaphors when describing their own knowledge — phrases like "mining crystal facts" appeared organically at every stage. I took that pattern seriously, formalized it, and it worked.

### 2. Excessive haste in answering

Reasoning has basic phases you have to walk through honestly: write out the task, identify and write out the method (the tool), assess confidence in every input you need for synthesis, identify the missing knowledge. At this stage I assumed I'd launch an internet search — I thought that was the only way to find a missing theorem or article. That's how `v2` got its **solution phases**, which `re!think` didn't have.

Both findings already paid off at this stage: separating the variables and the new confidence scale lit up the gaps in knowledge, and the phases helped diagnose those gaps reasonably well.

---

## Evolution of the versions

### v2 — phases and the density scale
The foundation was laid: variables `G/C/T`, the `Crystal/Liquid/Gas/Vacuum` scale, a phase structure, separation of Density across axes (including a dedicated `V` axis for verification), three generations of variables `_1/_2/_3`.

> Unfortunately, the reports from `v2` and `v3` experiments have not been preserved — I deleted them when rebuilding the test harness for the HLE run.

### v3.1 — Crystalline Descent

The main conceptual shift. After `v2` I started figuring out how to **pull knowledge out of the model's weights without resorting to internet search**, and that's where the method took shape:

**Crystalline Descent (Crystal Lensing)** — a way to extract, in fully correct form, knowledge that the model initially rates as `Liquid` or `Gas`. The model starts from a fundamental axiom / definitions of the target domain and step by step gets closer to the needed weights, writing out into output tokens the most crystal-reliable theorems along the way. That way the model increasingly **brings out the connections of the target domain** and, no less importantly, **damps down the noise from the weights of all other domains**.

In practice, even `Haiku` and `Gemini Flash` turned out to be capable of writing out — clearly and correctly — theorems they had seen in training only a couple of times, theorems that were completely inaccessible to them even after a request to take on a maximally precise role.

Once the method took shape, I realized that **any theorem can be retrieved from the weights**. From then on the work kept circling around how to write it out properly — getting not just the formula or the main statement, but the boundary conditions and the criteria by which the theorem sometimes has to be discarded.

> This mechanic has been extracted into its own folder [`mechanics/crystal_lensing/`](mechanics/crystal_lensing/).
>
> - **Mechanic description:** [`mechanic_crystal_lensing.md`](mechanics/crystal_lensing/mechanic_crystal_lensing.md) (en) · [`mechanic_crystal_lensing_ru.md`](mechanics/crystal_lensing/mechanic_crystal_lensing_ru.md) (ru)
> - **Benchmarks** — the same HLE tasks were run with and without the protocol, showing the essence and operability of the mechanic: [`benchmarks/`](mechanics/crystal_lensing/benchmarks/)
> - **Summary report** on the benchmarks: [`report.md`](mechanics/crystal_lensing/benchmarks/report.md) (en) · [`report_ru.md`](mechanics/crystal_lensing/benchmarks/report_ru.md) (ru)

After publishing this repository, I found independent researchers who had arrived at the same conclusions. ["Thinking to Recall: How Reasoning Unlocks Parametric Knowledge in LLMs"](https://arxiv.org/abs/2603.09906) (arxiv 2603.09906, March 2026) finds that generating topically related facts creates a semantic bridge to the target fact — what the authors call "factual priming", explicitly linked to the spreading activation theory from cognitive psychology. Their key experiment: extracting the intermediate facts generated during reasoning and re-injecting them as plain context recapitulates nearly all the recall improvement — confirming that the gain comes from the contextual neighborhood built around the target, not from logical inference. This is the same mechanism Crystalline Descent formalizes: filling the context with stepping stones anchored to the target domain narrows activation toward the knowledge that direct queries cannot surface.

A complementary finding comes from ["Empty Shelves or Lost Keys? Recall Is the Bottleneck for Parametric Factuality"](https://arxiv.org/abs/2602.14080) (arxiv 2602.14080, 2026), which systematically separates facts that are genuinely absent from weights ("empty shelves") from facts that are stored but unreachable via standard querying ("lost keys"). Their finding: frontier models retain 95–98% of tested facts in their weights — the bottleneck is access, not storage. My own observation, working across the full capability range of models, extends this claim: I believe even the smallest and weakest models — Haiku, Flash, local 7–30B — retain 90–95% of their training knowledge in their weights. What appears as blurry or degraded knowledge after pretraining is not missing knowledge. It is knowledge that has not been reached yet. Crystalline Descent is a method for reaching it.

Beyond the Descent, `v3.1` also brought:

- **Expanded Delta diagnostics** — what's missing for synthesis. Delta is projected onto `C` and `T`, and a **synthesis roadmap** is built (a work order / reasoning algorithm). This plan is verified first, then executed — without losing it during execution and without drifting sideways from the algorithm.
- **A mechanism for "releasing Attention tension"** — not via the word "forget", but **through inversion**. New tokens null out the influence of unneeded reasoning branches, leaving only the "brilliant" itself (the key theorem and its justification) and the data for synthesis. This was needed because the Descent really packs the context full.

At this stage I was still targeting the smaller models (`Haiku`, `Gemini Flash`), and the algorithm grew cognitively heavy. `Haiku` started slipping: sometimes forgetting the phase mid-solution, sometimes getting stuck inside the Descent.

### v3.6 — structure, vectors, skill library

To make the algorithm hold up against attention drift, in `v3.6` I first rewrote the **structure of the protocol**:

- Split everything into sections (vectors and sub-vectors).
- Aligned the protocol's wiring with the solver's predefined qualities — **gravitational channels** (attentiveness / accuracy / self-criticism).
- Pulled out a separate **skill library**, and inside the phase descriptions left only what's essential to that phase. The algorithms were rewritten to resonate better with the course of the solution: the moment the model writes out a phase tag during execution, it spotlights exactly the part of the system prompt that's needed right now, and from there spotlights the relevant skills.

This noticeably improved how well `Haiku` followed the protocol.

At the same stage I seriously expanded the **confidence assessment** approach: aggregate state of knowledge + control over assumptions. In experiments, Crystalline Descent already pushed the models to start diagnosing **"absence of knowledge"**: in places where the model had initially marked `Gas`, on close approach it often ran into **Vacuum** — no bridge to the needed theorem in the weights at all. A small but important victory: clear certainty that you don't know is more valuable on tasks like these than a hallucinated solution. I kept this success as a natural path back toward internet-search tools — though search itself on these protocol versions remained more of a leftover from earlier versions than the main path I was working on.

> Brilliant Topology (a classifier for how to describe extracted knowledge — facets, supports, boundary conditions, conceptual twins) is worked out in detail in `v4` and extracted into its own folder [`mechanics/topology_of_diamond/`](mechanics/topology_of_diamond/).
>
> - **Mechanic description:** [`topology_of_diamond_en.md`](mechanics/topology_of_diamond/topology_of_diamond_en.md) (en) · [`topology_of_diamond_ru.md`](mechanics/topology_of_diamond/topology_of_diamond_ru.md) (ru)

### v4.1 — spiral, brilliant topology, fighting tunnel vision

In `v4` I wanted to work through several problems:

**Geometry of the knowledge graph.** The protocol needed to be redesigned so it would feel **spiral-like** to the model: each phase engages the core habits in turn (attentiveness / accuracy / self-criticism), using progressively more data and tightening the quality bar.

**Brilliant Topology.** The earlier requirements for brilliants fit some tasks (physics and exact sciences) but were just plain wrong for others (biochemistry, behavioral and humanities-style disciplines). I worked out and described, in a separate file — [topology_of_diamond_en.md](mechanics/topology_of_diamond/topology_of_diamond_en.md) — the full topology of brilliants, so the model can write out from its weights not only theorems and formulas, but also hypotheses and observations. And I integrated this into the `v4` protocol.

**Tunnel vision.** This is the main problem I've been fighting since `v3`. In `v4`, to deal with it, I added the requirement to **project every facet of a Brilliant**: onto its native domain / onto the tool / onto the pressure on the answer. And to more cleanly separate **fragility points** as a distinct thing from assumptions (this particular piece hasn't yet worked the way it should — it still needs more work).

Also in `v4` I confirmed that the aggregate states `Liquid` and `Gas` only show up when you try to pull knowledge out **"from a distance"** — i.e., when no Crystalline Descent has been done. When the model walks step by step from axioms, theorem by theorem, it's left with **only two options**: "I know" (**Crystal**) and "I don't know" (**Vacuum**).

---

## Interim findings

- **Density-of-knowledge assessment `Crystal/Liquid/Gas/Vacuum`** works an order of magnitude better than the ordinary Confidence Score. Especially when broken down by task components (`Context / Goal / Tool / Role`, etc.). The final formula in the answer needs to be set **algorithmically** — explicitly showing that assumptions must be marked, and that it's exactly those that are subtracted from a baseline confidence of `1`.  
  *Empirical evidence and case analysis:* mechanic description — [`mechanic_confidence_by_density.md`](mechanics/confidence_by_density/mechanic_confidence_by_density.md) (en) · [`mechanic_confidence_by_density_ru.md`](mechanics/confidence_by_density/mechanic_confidence_by_density_ru.md) (ru); benchmarks report — [`report.md`](mechanics/confidence_by_density/benchmarks/report.md) (en) · [`report_ru.md`](mechanics/confidence_by_density/benchmarks/report_ru.md) (ru).

- **Crystalline Descent works on all models.** It's especially important for `Haiku / Sonnet / Gemini Flash` — on these models most knowledge is initially in a `Gas` / `Liquid` state. On frontier models (`Opus`, `Gemini Pro`) the mechanic gives a lift too, though it's needed less often: their baseline rating is more often `Crystal` or `Liquid`, and `Gas` barely shows up — for them `Gas` usually means "on close approach we'll run into Vacuum". Even without internet search, models pull out most of the formulas they had seen only a couple of times in training. And this is **the ideal way to diagnose the delta** and put together a targeted internet query.  
  *Empirical evidence and case analysis:* mechanic description — [`mechanic_crystal_lensing.md`](mechanics/crystal_lensing/mechanic_crystal_lensing.md) (en) · [`mechanic_crystal_lensing_ru.md`](mechanics/crystal_lensing/mechanic_crystal_lensing_ru.md) (ru); benchmarks report — [`report.md`](mechanics/crystal_lensing/benchmarks/report.md) (en) · [`report_ru.md`](mechanics/crystal_lensing/benchmarks/report_ru.md) (ru).

- **The algorithm is cognitively heavy for `Haiku`.** I hit the ceiling of what it can do — it has few Attention Heads. The protocol works substantially better on `Sonnet` and `Opus`, but I don't have enough budget for large-scale experiments on those models (a single task can run up to **70,000 output tokens**).

- **Tunnel vision is the main unresolved problem right now.** I'm confident the protocol on `Sonnet` / `Opus` will give a boost on HLE, but tunnel vision (when the model has already invented an answer for itself and then blindly confirms it) hasn't been beaten yet. On `Haiku` this exact problem killed attempts even when the model had correctly written out the formula and theorem, and then "forgot" or "didn't want to" write out the boundary condition that invalidates the theorem in the task. `Sonnet` is exposed to this too.

- **The structure of the protocol still needs polishing.** How it's organized (word order, grouping of sections, structuring of meanings) is what determines first of all whether **the model holds the algorithm from the start of the solution to its end**.

- **Potential boosts** will come from (a) **internet search tools** — I'll bring them back once I finish fitting the current version to the models; (b) **orchestration** — an internal state parser that injects phase-specific instructions before each next step. That's no longer a "single-beat" run, but it's still execution without a user in the loop, and still an algorithm aimed directly at the LLM.

---

## Main takeaway

Once development of the protocol is finished, this will be a ready **framework for building a dataset of reasoning examples**. The protocol will show its full strength once it's **integrated into model weights at the pretraining stage** — at that point models will be able to execute it substantially more reliably and with better quality than by reading it from a system prompt.

---

## Protocol versions

- [`re-solve_v2_en.md`](prompts/re-solve_v2_en.md) — phases and the density scale
- [`re-solve_v3_1_crystall_lens_and_diamond_ru.md`](prompts/re-solve_v3_1_crystall_lens_and_diamond_ru.md) — Crystalline Descent, Brilliant, Delta, and roadmap *(Russian only)*
- [`re-solve_v3_6_vector_and_skills.md`](prompts/re-solve_v3_6_vector_and_skills.md) — vector structure, skill library
- [`re-solve_v4_1_spiral_phases_en.md`](prompts/re-solve_v4_1_spiral_phases_en.md) — spiral-like architecture, Brilliant topology, facet projections

Related file:
- [`mechanics/topology_of_diamond/topology_of_diamond_en.md`](mechanics/topology_of_diamond/topology_of_diamond_en.md) — classifier of Brilliant facets and topologies
