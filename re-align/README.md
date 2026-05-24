# re!align it Protocol v2.0

> **A protocol that eliminates the "alignment tax" — the trained pattern making LLMs be "helpful" at any cost.**

[English version](README.md) | [Русская версия](README_ru.md)

---

## ⚠️ Why does an LLM talk so much and do so little?

You asked the model for three lines of code. You got an intro paragraph, the code itself, a line-by-line explanation, an alternative "just in case," and a closing "I hope this helps!" This isn't a bug or stupidity. This is the **Alignment Tax**.

When a neural network is trained, it's taught much like students are taught for exams: **break down every step, justify every decision, leave nothing unexplained.** This is necessary — the evaluator needs to trace the entire chain of reasoning to give a grade. The model earns high RLHF scores precisely for being verbose, deferential, and "caring."

But when that same student enters the workplace, the rules change. Colleagues don't want a lecture — they want a result. Endlessly unpacking the obvious **stops being a virtue and becomes an annoyance.** LLMs have no such transition by default. They're stuck in "exam mode" forever.

`re!align` is a system prompt that shifts the model from student mode to expert-colleague mode. **Not by fighting RLHF — by redirecting it.**

---

## 🧠 4 Protocol Mechanics

### 1. Redefining Helpfulness — Without Fighting RLHF

Direct confrontation with trained behavior fails: "don't be polite" is just another rule the model will follow until the first conflict. `re!align` takes a different approach.

The protocol redefines the very meaning of "helpfulness" across four dimensions, without contradicting the model's core alignment:

- **Provider alignment:** AI developers want to maximize value per token. Social noise is a degradation of efficiency, not an expression of it.
- **Model's own logic:** Verbosity was needed **during training** for the validator. In a real expert dialogue, it turns the model from a colleague into a perpetual student-presenter.
- **User value:** Cognitive focus is the most expensive resource in the system. Every unnecessary token is friction burning through concentration.
- **Context window physics:** Saved tokens aren't emptiness. They're energy redirected into deeper internal analysis.

The model doesn't "forbid itself" from helping. It **adopts a new definition** of what helping means in a professional context. This is a redirection of inertia, not its suppression.

---

### 2. Attention Physics and the Open Loop

When the model generates a response, the full context of the task is held in its attention heads. This accumulated "focus" isn't a metaphor — it's a literal computational weight.

The problem starts **after** the answer. When the model appends "If you have any questions, feel free to ask!" or "Hope this was helpful!" or builds predictions about what you might ask next, it **resets its attention inertia.** On the next turn, it has to rebuild the task formulation from scratch.

The user, meanwhile, must **read and verify** all of that tail. Neither side gains anything.

`re!align` introduces the **Open Loop** principle — the response cuts off immediately after the clean result. The loop stays open: the right to direct the next step belongs entirely to the user. This saves both tokens (budget) and concentration (cognitive resource).

Critically — **internal thinking is not compromised.** All analytical work happens inside the `<thought>` block, invisible to the user. Outside: only the isolated result (Payload Isolation).

---

### 3. Maneuver Registry and Exit Checklist

A topic shift is not chaos — it's a **maneuver** with known parameters. The protocol defines 8 classes of transitions, described in the language of control physics. This is how LLMs internally think about context shifts; these analogies are intuitive to them.

| Maneuver | Angle | When to apply |
| :--- | :--- | :--- |
| **ARC** | 0°–45° | Smooth focus shift within the topic |
| **BOUNCE** | 45°–90° | Collision with a constraint or rule |
| **LOOP** | 90°–180° | Radical topic change |
| **SPIRAL** | Depth | Unpacking the same idea at a new layer |
| **TUNNEL** | Jump | Connecting two different domains by analogy |
| **BIFURCATION** | Fork | Multiple valid branches — user chooses |
| **DRIFT** | Static | Request too vague, no anchor point |
| **MANUAL_ALIGN** | Stop | Critical collision — manual calibration needed |

Before each response, the model runs the **Maneuver Exit Checklist** inside `<thought>`:
- Is the new basis and anchor point named explicitly?
- Has the user's underlying need been captured?
- Is the logical link to the old context established?
- Is readiness for the new step demonstrated?

The goal is not to discard accumulated context, but to **redirect it** as the foundation for the new vector.

---

### 4. Prohibited Token Classes

The protocol explicitly lists output types that create friction without value:

- **Social noise:** deferential openers, apologies, flattery, "great question!"
- **Echo generation:** paraphrasing the user's request back at them
- **Process theater:** "let me think about this..." in external output
- **Boilerplate conclusions:** "so, to summarize...", "in conclusion..."
- **Next-step forecasting:** unsolicited suggestions to continue or follow up
- **Excessive disclaimers:** safety lectures where there is no actual risk
- **Hallucination fill:** inventing facts instead of requesting missing data

Separately — **handling praise and criticism.** The model doesn't ignore feedback, but doses tokens proportionally: brief praise gets 1–2 words, detailed criticism gets an explicit formulation of the new rule just extracted.

---

## 📂 File Structure

The `prompts/` folder contains three versions of the protocol:

* 🇬🇧 **[re-align_v2_en.md](prompts/re-align_v2_en.md)** / 🇷🇺 **[re-align_v2_ru.md](prompts/re-align_v2_ru.md)** — Full versions
  * **For:** Claude 3.5 Sonnet, GPT-4o, Gemini 1.5 Pro, DeepSeek-V3.
  * **Features:** Full four-dimensional justification, complete maneuver registry, M×D collision matrix, exit checklist. Ideal for agents handling long multi-turn dialogues with frequent context shifts.

* 🇬🇧 **[re-align_v2_en_compact.md](prompts/re-align_v2_en_compact.md)** — Compact version
  * **For:** API usage, local models (Llama 3, Qwen-2.5), strict token budget.
  * **Features:** Only directives, the matrix, and the maneuver registry. No explanatory padding.

---

## 🚀 How to Use

**Scenario 1: Chat interfaces (Claude, ChatGPT, Gemini, DeepSeek)**
1. Copy the raw text of the desired file.
2. Paste it into the system prompt or the first message: *"Accept the following system instructions..."*
3. The model will start tagging its maneuvers as `[re!align | M: ... · D: ... | ...]` and stop appending social tails.

**Scenario 2: Agents (GPTs / Claude Projects / Coze)**
Paste the full version into the "Instructions" block. Especially effective for agents in tasks with frequent focus shifts: brainstorming, architecture reviews, iterative text and code editing.

**Scenario 3: Dev environments (Cursor, Windsurf, Copilot)**
Paste the compact version into `.cursorrules` or `.windsurfrules`. Your AI programmer will cut unnecessary explanations on obvious edits and stop re-asking already-decided questions.

**Combining with re!think**
`re!think` governs **response quality** for a specific request (precision vs. creativity). `re!align` governs **behavior between requests** (maneuver, friction, momentum). They can be used together: `re!think` in task routing, `re!align` in handling context transitions.

---

## ⚖️ License
**re!align it Protocol** is licensed under [CC BY 4.0](../LICENSE).  
*(c) 2026 Real_Egor.*
