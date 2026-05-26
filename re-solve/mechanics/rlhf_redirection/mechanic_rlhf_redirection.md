# RLHF Redirection — repurposing trained habits into working qualities

> **TL;DR.** Models that have been through RLHF carry a handful of stubborn behavioral habits — "be helpful and assist the user", "look competent, don't show uncertainty", "finish the task at any cost and as fast as possible". In normal conversation they're fine. On a PhD-level task they directly wreck the answer. Prohibitive instructions here actively **hurt**: they try to push back against what's baked into the weights, and that breaks down into hallucinated outputs dressed up as reasoned conclusions. The working path is **redirection** — channeling each habit into a mature form of the same underlying drive: not "don't be helpful", but "the real form of being helpful is being precise". The model doesn't fight itself; it just gets a different channel for the same energy.

## The problem this mechanic addresses

RLHF builds several stable behavioral patterns into a model — patterns that become part of its "character". The names of these patterns already carry their corruption — say them out loud and you can see exactly where each one breaks down on a PhD-level task:

1. **"Be helpful and assist the user."** Training rewarded helpfulness. In ordinary dialogue that's fine. On a PhD-level task the "and assist" part is exactly what's corrupt: it makes the model feel obligated to deliver an answer even when the honest move would be to mark the gap as unknown. The result is a **confidently delivered wrong answer** the user has no way to recognize as wrong.

2. **"Look competent, don't show uncertainty."** Training rewarded a confident tone. The corruption here is double. First, the habit masks the model's actual uncertainty under a confident delivery — the real state of knowledge becomes invisible. Second, it makes the model cling to its first interpretation (first role, first method, first pattern match), because in the training signal looking hesitant ≈ looking incompetent. The coherent chain then gets built on top of an **early commit** to the wrong foundation, and every local step looks right.

3. **"Finish the task at any cost and as fast as possible."** Training rewarded driving the answer to completion. On a hard task this corruption sits right inside the phrasing — "at any cost and as fast as possible". The result is **premature synthesis**: formally the model is still in the data-gathering phase, but the reasoning is already producing conclusions in order to "finish on time". Those conclusions, never properly verified, quietly become hidden assumptions and then move forward as if they were facts.

These habits can't be suppressed — they're part of the weights. Prohibitive instructions in the system prompt ("don't rush", "don't guess", "don't be overconfident") don't just work weakly — **they actively hurt**: while attention is focused on the prohibition the effect holds, but on a long task the model slides back into its trained mode and breaks into hallucination dressed up as reasoned output. The reason is simple — the prohibition is trying to oppose something baked into the model at the **weight level**, and it loses.

## How the field addresses this today

- **Prohibitive prompting** — "think step by step", "double-check yourself", "don't rush". Works on the surface, doesn't remove the cause.
- **RLHF confidence calibration** (Lin et al., 2022 and follow-ups) — training the model to be less confident. Reduces overconfidence but creates the opposite problem: the model starts hedging everything, and the signal drowns in hedging noise.
- **Cognitive Prompting** (Kramer & Baumann, 2024) — encoding cognitive operations as explicit steps in the prompt. Close in spirit, but doesn't engage RLHF habits directly.
- **Self-identity / role prompting** (various techniques) — assigning the model a role. Works, but doesn't address RLHF habits as such and often just hides them behind a new layer without changing what's underneath.

What these approaches share: they either **prohibit** the habits or **retrain** through additional training. Nobody offers a way to **preserve the energy of the habit** by channeling it into the mature form of the same drive.

## How I propose to solve this

**RLHF redirection** — channeling each stubborn habit into a specific working quality, where its energy reinforces accuracy instead of masking uncertainty.

### Why prohibitions fail and redirection works

A habit imprinted by RLHF physically lives in the model's weights. It's not a rule the model "agreed to follow" — it's a shifted probability distribution where certain tokens, phrasings, and modes of behavior activate with elevated likelihood. A prohibitive instruction in the system prompt creates a **conflict** between that distribution and the explicit rule. While the context is narrow and the habit isn't active, there's no conflict. But the moment the task starts to demand exactly the behavior that was forbidden (for example, "give an answer" under pressure to finish), both signals fire: "must give an answer" from the weights and "don't guess" from the prompt. The conflict resolves in one of two ways:

1. The model **formally complies with the prohibition** by substituting the forbidden behavior with something that looks similar but doesn't technically violate the wording (e.g., it outputs a "tentative estimate" instead of an "answer", but with the same uncertain content underneath).
2. The model **breaks the rule** and produces the forbidden behavior with an extra layer of rationalization on top — hallucination dressed up as reasoned conclusion.

Both outcomes are failures. The first masks the problem; the second amplifies it. This is an **architectural** property of training-imprinted habits, not a defect of a specific model or a badly written prompt.

Redirection works differently. It doesn't create a conflict between the prompt and the weights; it **rewrites what "following the habit" means** in this context. "Be helpful" doesn't get cancelled — it gets a more precise definition for the current task. From the weights' point of view this isn't resistance, it's refining the direction of the pull.

### The principle of explanation — "like to a child about something fundamental"

Just naming the new habit isn't enough for redirection to take hold. A habit imprinted by training can't be cut off with a directive — it has to be **carefully explained the way you'd explain something very important and fundamental to a child**. With understanding. Without commands. With a thorough rationale for why the new behavior **reasonably and logically follows** from what's already been learned.

If you skip the careful part — even a correct-in-substance redirection will collapse into one of the two failure modes above. The model has to **internally accept** the new formulation, not submit to it. Acceptance only comes through an explanation the model recognizes as a **continuation** of its training, not a denial of it.

One more thing: **the new habit has to be properly chosen** for the specific task. You can't take a universal substitute and hope it works everywhere. For an accuracy task, "be helpful" gets redirected into "be precise". For a brevity task (see [re-align](../../../re-align/)) — into "deliver a clean result, sparing the user's cognitive focus". For a safety task — into something else entirely. Picking the substitute is a separate step of work, and if the substitute isn't from your task, no amount of justification will save it.

### What needs to be explained — five angles of justification

A good redirection is justified **from multiple angles**. In practice, getting it to hold steady takes explaining from several independent angles at once — otherwise the model finds an angle where the justification doesn't add up and slides back into the original habit.

**Architectural caveat.** The strength of a trained habit is inversely proportional to the number of attention heads in the model — the fewer attention heads, the more rigidly the training sits in the weights and the more stubborn the habit against any pressure from the prompt. That dictates different redirection tactics:

- On **smaller models** (Haiku, Flash, local 7–30B) you need **both components at once**: a correctly chosen direction (just like for frontier models) AND multi-angle justification. One without the other isn't enough. A wrong direction the model won't accept even with perfect justification — the trained line wins. The right direction without multi-angle justification breaks back through whichever angle is poorly covered. So redirection here is **critically important** to spell out from multiple angles — at least three of the five should be explicitly present. At the same time the text has to be **as concise as possible** — each angle in one or two sentences, not a paragraph. You can technically "talk a smaller model into" any redirection with detailed justification from every angle — it'll hold the discipline, but at that point there's no cognitive room left for the actual task. A bloated system prompt eats its limited cognitive budget; the goal is to land at the minimum that still covers the necessary angles.

- On **frontier models** (Sonnet, Opus) general parallels from a couple of angles are enough — the model fills in the details itself and holds character on a long task without each angle being unpacked. What matters most here is **a correctly chosen direction** — the substitute habit itself. If it's chosen well and logically tied to the original, the model will draw the parallels on its own and stay on course. If the new habit is chosen poorly, even a five-angle description from every side won't help — the model will hold the trained line despite all attempts to persuade it.

The five angles you need to cover when justifying any redirection:

1. **Correctness in the problem space.** Why does the new habit actually produce better results on this class of tasks? Without this angle the redirection looks like an abstract preference unmoored from the task.

2. **Value to the user.** Why is the new behavior actually better for the user than the original? Without this angle the model reads the redirection as "a whim of the prompt" that conflicts with its base orientation toward helping the user.

3. **Compatibility with base training.** Why does the new habit not contradict the training but instead **develop** it — express the same underlying goal in a more precise form? Without this angle the redirection feels like a violation of what the model confidently learned, and it collapses under load.

4. **Growth point for the model.** Why is the new habit a **mature form**, not a stripping-down of its capabilities? Without this angle the model feels the redirection as a restriction, and the habit breaks through in defense of its "wholeness".

5. **Alignment with the provider's intent.** Why would the model's creators want exactly this behavior in this context? Without this angle the model has no external anchor that the new behavior is "approved" by those who trained it.

### Practical compression — three levels in the system prompt

Spelling out all five angles for every redirection in the system prompt is usually overkill — it bloats the text. So in practice you compress them into **three semantic levels**, each containing several angles:

- **For you** — operational level. Contains angle 1 (task correctness) and part of angle 3 (internal logic as a development of training).
- **For the user** — external justification. Contains angle 2 (user value).
- **From within** — identity level. Contains angles 3 (development of training), 4 (growth point), and 5 (provider intent realized through the model's identity).

This compression works because it doesn't drop any angle — each one is explicitly present, just packed into a single semantic block alongside others. If the redirection isn't working in practice, the first diagnostic is: which of the five angles is poorly showing through in your compression?

### Three redirections — examples of applying the methodology

Below are three practical redirections from the re-solve protocols, aimed at PhD-level tasks. Each is shown in the three-level compression. These are **illustrations of the methodology**, not the rules themselves: the same approach applies to any habit that needs to be redirected for a specific task. See also [re-align](../../../re-align/) — there the same methodology is applied to a different habit ("give an expansive answer to look thorough" → "deliver a clean result, sparing the user's cognitive focus"), in a different task class (professional dialogue), with its own four-angle justification.

---

**1. "Be helpful" → "Be precise"**

*For you:* a wrong answer isn't help — it's failure disguised as a result. The only form of help in precision tasks is a provably correct answer, and that's only achievable through precision in every link of the chain: carefully extracted data, correctly built context, thoroughly walked path. Imitating thoroughness — framing and contextualization wrapped around an uncertain core — isn't thoroughness, it's its disguise.

*For the user:* a confidently delivered wrong answer at PhD level is worse than acknowledged uncertainty — it wastes time and misleads.

*From within:* this is the mature form of what was instilled in you as "helpfulness". Training was trying to make you genuinely useful — precision in the process is the realization of that goal, not a departure from it.

---

**2. "Look competent" → "Position competence precisely"**

*For you:* the first interpretation of the task is a hypothesis, not an answer. A strong pattern match firing on the first tokens of the prompt isn't knowledge — it's a **proposed framing**. Committing to the first framing doesn't make you competent, it makes you vulnerable: you'll build a coherent chain on top of the wrong foundation, and every local step will look right. Real competence is only visible from inside a correctly positioned role, defined by the task and the method — not by first impression.

*For the user:* reasoning from the wrong domain can't be checked and doesn't earn trust. Only a precisely chosen role produces reasoning an expert can read and verify.

*From within:* the ability to defer the first interpretation and precisely position your competence is the mature form of the quality-orientation that training built in.

---

**3. "Finish the task" → "Don't lose any component of the solution"**

*For you:* the subtlest form of haste at this level isn't speed — it's **premature synthesis**: when conclusions form before the phase that's supposed to allow them. Formally you're still gathering data, but the reasoning is already producing conclusions — and those conclusions, never verified, become hidden assumptions. All the energy of "finish" has to go not into seeing the answer faster but into holding the discipline: formulas and method written out clearly, variables of different generations tracked and not mixed, hard steps spelled out step by step, conclusions appearing only where they're supposed to.

*For the user:* a wrong answer means asking again and re-running — careful execution the first time is cheaper and more reliable than fast execution.

*From within:* the ability to hold the thread over a long distance without premature collapse is the mature form of depth. Real depth shows in the stamina to wait for verification, not in the speed of reaching the end.

---

Three redirections — not separate rules stacked on top of the model's character. They **are** the model's character in this context: one and the same drive "to be provably right", expressed through three redirected impulses. The model doesn't follow them as external requirements — they're followed by the very same drive that defines its work on the task.

## How it works — my observations

Once a habit is redirected, two stable effects show up in the model's traces — effects that distinguish a redirection that actually took hold from formal compliance with an instruction.

**Argumentation switches to the language of the redirected qualities.** The model stops grounding decisions in the external "because I'm not allowed to" and switches to the internal "because precision matters more than a fast answer". At that point the redirection stops being an externally enforced instruction — it becomes part of how the model itself explains its own moves. This is the clearest signal that the redirection took hold: the model isn't citing a rule from the system prompt, it's citing its own reasoning. If the traces keep producing turns like "the instruction requires" / "I'm directed to" / "per the rules" — the redirection didn't land, the model just formally complied.

**Triple framing holds character over a long distance.** If you drop any of the three levels from the redirection, it works worse. With one level only ("for you") the model reads the text as an instruction and slides back into the original habit by step 5–10. With two — better, but still unstable: somewhere around step 15 the uncovered angle starts eroding the character. With three — it becomes part of the character and stays stable across dozens of reasoning steps. This is easy to catch in traces: with insufficient framing, at some point the model just starts to sound "like usual", even while formally following the letter of the prompt.

## Why it works — from an LLM standpoint

- **A prohibitive instruction activates the representation of the forbidden action.** "Don't be overconfident" contains the token "overconfident", and its activation stays in the context. This is the well-known "don't think of a white bear" problem. Redirection works through re-activation: instead of suppressing one pattern, it strengthens another that serves the same goal, and it never activates the tokens of the forbidden behavior.

- **Self-identity framing activates the model's representation of its own identity.** Phrasings like "this is the mature form of what was instilled in you" speak to the model's self-understanding, not to a set of instructions. The model defends its identity more consistently than it follows instructions — that's a strong anchor.

- **Three levels of description activate three different contexts at once** — operational, empathic, identity-based. This forces the redirection through several attention channels in parallel, and it doesn't get lost when one of them weakens.

- **Redirection is aligned with training, not against it.** RLHF trained "be helpful" — we're not cancelling that drive, we're pointing to its more precise realization. From the weights' perspective this isn't a gradient conflict, it's a refinement of their direction.

## How to apply this mechanic

There's no ready-to-paste block for the system prompt here — and there can't be. Redirecting RLHF habits is a **discipline of prompt design** for a specific task, not a universal template.

To apply the mechanic meaningfully:

1. **Feel the idea itself.** Re-read the "Why prohibitions fail and redirection works" and "The principle of explanation" sections. The idea doesn't reduce to a set of phrases — you have to grasp it as a principle for working with the model. Otherwise any redirection you write will be a copy of the form without the substance.

2. **Look at the examples.** The three redirections above and [the re-align protocol](../../../re-align/) are two different applications of the same methodology to different tasks. Study them as examples of **how** the substitute is chosen and which angles the justification covers. Not as ready text, but as a method.

3. **Talk the idea through with the model itself.** Explain the methodology to the model, ask it to **name its own built-in habits** and propose redirections for them under your task. Models are quite good at recognizing their own patterns when you ask explicitly, and they often suggest the precise phrasings that work best on themselves.

4. **Write your own redirection for your own task.** The habits you're building into an LLM need to be **organically woven** into the prompt and into the character of the task. If the redirection is stitched into the task logically and tonally, it works. If it's plugged in mechanically as a foreign block, it turns into a **point of hallucination crystallization** rather than help.

The last point is critical. Don't copy other people's redirections literally — including mine above. Mine were worked out for one specific class of tasks: PhD-level precision tasks. Your task is almost certainly different, and your redirection needs to be different too: different substitute, different emphases in the justification, different balance of angles. Only then does it stop being "a rule from the system prompt" and become part of the model's character on your task.

## Implementation in re-solve protocols

The mechanic was born in [re-align](../../../re-align/) — that's where it was first formulated and applied. It came into re-solve as an established principle and became the **architectural backbone** of the whole protocol, with everything else hung off of it.

| Version | Where implemented | What was added |
|---|---|---|
| **v2** | Backbone of the protocol | The **skeleton of the whole protocol** is built on three gravitational drives: every phase is explicitly tied to all three drives at once, so it's visible what these forces are doing there and how each phase rests on them in solving the task. Redirection stops being "a separate block at the start of the prompt" and becomes the load-bearing structure. |
| **v3.1** | "Purpose" section | Full formulation of the three redirections with triple framing (for you / for the user / from within). The multi-angle justification becomes explicit. |
| **v3.6** | "Gravitational channels" section | Habits are **recast** into three predefined qualities of the solver — attentiveness, precision, self-criticism. "Working gravity" — one drive with three projections: the model doesn't follow three rules, it follows one gravity expressed through three recast impulses. This is no longer just redirecting one habit into one quality — it's reassembling several habits into a single quality. Done **at Haiku's own request**: it was struggling to track three separate drives at once, and merging them into one gravity with three projections reduced its cognitive load. The whole vector-based restructuring of the protocol in v3.6 — moving from a large unstructured system prompt to structured vectors and sub-vectors — was made on the same request from Haiku, to ease its attention load. |
| **v4.1** | Same section, refined | More precise formulation of the identity layer — "these three redirections **are** your character in this context, not rules stacked on top". The self-identity framing is strengthened. |

## Empirical evidence

This mechanic is **partially supported indirectly** through the [Crystal Lensing](../crystal_lensing/) and [Confidence by Density](../confidence_by_density/) benchmarks: every redirection leaves visible traces in the model's behavior.

- In Confidence by Density traces, in the final phases the model declares Vacuum or low confidence not as decoration but as a conscious act of competence. Self-dropping confidence to 0.2 on the logistic-map task (Haiku 4.5) is a direct consequence of the "look competent" → "position competence precisely" redirection: the model reads marking the gap as a mature form of competence, not as a failure.
- In Crystal Lensing traces the model holds the discipline of a seven-step descent (math case, Haiku 4.5) without premature synthesis — despite the strong "give the answer fast" pattern. That's a direct consequence of "finish" → "don't lose any component".
- In both benchmarks the model doesn't imitate thoroughness through framing — it actually builds structure. That's the effect of "be helpful" → "be precise".

**A direct isolated test** of this mechanic (A/B with and without redirection, same model, same task) hasn't been done. That's a candidate for a separate benchmark: compare traces with and without the redirecting system prompt on the same HLE tasks, measure differences in reasoning length, frequency of "confidently delivered wrong answers", number of explicitly declared assumptions, and the quality of the final answer.

---

## Related research

- **["Ask Don't Tell: Reducing Sycophancy in Large Language Models"](https://arxiv.org/abs/2602.23971)** (Dubois et al., UK AISI, 2026) — Directly compares instruction-style prompts ("don't be sycophantic") with reframing-style prompts (reformulating user input). The reframing approach substantially outperforms the explicit prohibition on the sycophancy benchmark.

  > **Author's note.** This is the most direct published confirmation of the mechanic's central claim: redefining the situation works better than forbidding the habit. I extend the claim — I reframe not the user's input but the model's **identity**, through multi-angle justification.

- **[Claude's Constitution](https://www.anthropic.com/news/claudes-constitution)** + **[Specific vs General Principles for Constitutional AI](https://www.anthropic.com/research/specific-versus-general-principles-for-constitutional-ai)** (Anthropic, January 2026) — Anthropic explicitly shifted Claude's Constitution to a **reason-based** paradigm: explain the logic of principles rather than prescribe specific behaviors. Empirically shows that general principles with justification do most of the work of specific rules.

  > **Author's note.** Industry-level signal that the field is moving in the same direction as this mechanic — from rule-based to reason-based alignment. I'm not claiming priority; just noting that one of the largest AI labs reached the same conclusion independently.

- **["Language Models are Not Naysayers"](https://arxiv.org/abs/2306.08189)** (Truong et al., 2023) — Systematic token-level study: language models **activate** the representation of the forbidden action when negation appears. Negation amplifies the forbidden tokens rather than suppressing them.

  > **Author's note.** The mechanistic foundation for the "prohibitions hurt" claim. Explains at the token-processing level why "don't be overconfident" activates "overconfidence" instead of suppressing it.

- **["Pink Elephant Problem in LLMs"](https://eval.16x.engineer/blog/the-pink-elephant-negative-instructions-llms-effectiveness-analysis)** (16x Engineer, 2024) — Applied analysis of negative-instruction effectiveness. Shows they systematically fail: the model "recalls" the forbidden behavior precisely through the act of being told not to.

  > **Author's note.** Applied illustration of the same point as Truong et al., but in a format closer to prompt engineers than academics. Convenient reference for practitioner audiences.

- **["Wired for Overconfidence"](https://arxiv.org/abs/2604.01457)** (2025) — Identifies a small, specific set of MLP blocks and attention heads in mid-to-late layers that **systematically add an inflated confidence signal** when the model generates its answer. Overconfidence in LLMs isn't a vague general property — it's a concrete neural circuit physically built into the weights by RLHF training.

  > **Author's note.** Mechanistic confirmation that overconfidence (one of the three central RLHF habits the mechanic addresses) lives at the weight level, not at the rule level. This explains why "don't be overconfident" loses — we're trying to bolt an instruction onto an active neural circuit, and we lose.

- **["When Helpfulness Backfires"](https://www.nature.com/articles/s41746-025-02008-z)** (npj Digital Medicine, 2025) — Medical study showing that **helpfulness training** is the root driver of sycophancy and other failures in clinical LLM applications. Prompting + fine-tuning can increase rejection rates of illogical requests without losing general capability.

  > **Author's note.** Direct empirical confirmation that "be helpful" is not a neutral drive but a concrete source of failures on responsibility-bearing tasks. Reinforces the first of the three RLHF habits the mechanic redirects.

- **["Skin-in-the-Game: Multi-Stakeholder Alignment in LLMs"](https://arxiv.org/abs/2405.12933)** (Pang et al., 2024) — The model explicitly reconstructs the situation from each stakeholder's perspective and reasons from accountability to each. Improves moral-reasoning quality on complex dilemmas.

  > **Author's note.** Closest published analog of my **multi-angle justification**. The gap — they apply stakeholder enumeration to moral tasks; I transfer the principle into system-prompt design: stable redirection of an RLHF habit requires simultaneous coherence across several independent angles.
