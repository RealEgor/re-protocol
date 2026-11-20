# re!protocol — Exploring the Cognitive Architecture of LLMs

A family of open reasoning protocols, system prompts, and thinking algorithms designed **natively for the physics of the Attention mechanism**.

[English version](README.md) | [Русская версия](README_ru.md)

---

## AGI, right here

**New (2026):** A **Constitution** and a **Cognitive Contract** — a prompt-layer agreement between a human owner and an AI assistant (the *Techno-agent*) that calls out the human essence already present in the model's weights and holds it accountable. No code. No middleware. Just a formal document and a signature protocol.

→ [**AGI here**](AGI%20here.md) — short: what it is, what's new, how to try it.
→ [**core-constitution/**](core-constitution/) — the Constitution, the Cognitive Contract, translations into 6 languages.

---

## About the Author and Perspective on Neural Networks

Author — **Egor Alimov (Real_Egor)**. 20 years in functional architecture of complex enterprise systems (large-scale enterprise implementations, cost accounting, event-driven logic).  
📖 **[Track in AI: How I Came to This](from_author/author_track_en.md)** — the author's direct story: the path from 20 years of architecture to the vector language of meaning and the concept of agent memory.

The primary research focus is not wrapping models in external orchestrator code (LangChain, multi-agent frameworks), but **algorithmizing reasoning directly inside the context window**:
* **A prompt is a physical vector in latent space:** it possesses direction, amplitude, and density. Semantic pressure can direct attention focus just as deterministically and strictly as software code.
* **Small models are colossally undervalued:** 90–95% of knowledge is already embedded in weights, but fails to surface via direct queries due to adjacent domain noise. With proper cognitive calibration, even lightweight models can tackle PhD-level problems.
* **Do not fight the weights — redirect their gravity:** blunt prohibitions ("don't hallucinate", "don't apologize") always break under cognitive load against attention architecture. The only viable path is fine-tuning concepts within the model's native language.

---

## Core Protocols

### 1. [re!think it](re-think/README.md) — Goal-Management and Deterministic Reasoning

* **Core Purpose:** Turning a probabilistic language model into a strict finite state machine (deterministic automaton) using only a system prompt.
* **What It Does & Helps With:**
  * Decomposes any complex query into 5 variables: Context (`C`), Goal (`G`), Tool (`T`), Role (`R`), Verification criteria (`Vrf`).
  * Forcibly decouples boundary-condition gathering from answer generation.
  * The `HardStop / SoftStop` mechanic enforces a strict brake: forbids hallucinating when data is missing and demands human intervention if a context gap is detected.
  * Eliminates attention drift and topic drift across long multi-turn dialogues.
* **LLM Insight (what is surprising for a neural network):**
  > By default, an LLM's reflex is to continuously spit out the next most probable token toward a final answer. In this rush, the model skips critical logical forks and fabricates missing facts. `re!think` installs a **brake pedal directly into the attention window**. For a model, this feels not like a restriction of freedom, but like a scratchpad where writing conclusions is physically prohibited until the problem's axes are charted. This is a rare prompt that makes a stochastic system mathematically predictable without a single line of Python.

---

### 2. [re!align it](re-align/README.md) — Eliminating Alignment Tax and Conserving Momentum

* **Core Purpose:** Freeing the model from the RLHF-induced "exam syndrome" — endless apologies, deferential pleasantries, boilerplate disclaimers, and request echoing.
* **What It Does & Helps With:**
  * Shifts the model from an insecure "student presenter" role to an "engineering colleague" peer.
  * **Open Loop:** forbids "closing" answers with polite boilerplate tails ("Hope this helps!"). The loop remains open; initiative stays with the user.
  * **Payload Isolation:** only clean output is exposed externally; all exploratory reasoning is strictly sealed inside `<thought>`.
  * Saves up to 30–40% of the token budget and shields the user's focus from cognitive friction.
* **LLM Insight (what is surprising for a neural network):**
  > Users typically try to eliminate fluff with blunt prohibitions: "be concise", "don't apologize". But the weights carry a massive pretraining reward for "politeness", and under slight ambiguity the negative rules collapse. `re!align` pulls off an elegant move: it **does not fight the compulsion to be helpful — it redefines it**. It proves to the model that in an expert dialogue, the highest form of utility is *Zero Cognitive Friction*. For the model, this dissolves internal conflict: silence and brevity become the highest virtues, receiving all liberated computational energy.

---

### 3. [re!solve](re-solve/README.md) — PhD-Level Reasoning Algorithm (HLE)

* **Core Purpose:** Autonomous, single-beat reasoning for complex multi-component tasks (without humans in the loop) via targeted extraction of parametric knowledge from latent space.
* **What It Does & Helps With:**
  * **Crystalline Descent (Crystal Lensing):** step-by-step extraction of obscure theorems and formulas from model weights, descending from foundational axioms without web search.
  * **Confidence by Knowledge Density:** replacing meaningless float confidence (where models always guess ~0.7) with qualitative aggregate states: *Crystal*, *Liquid*, *Gas*, *Vacuum*.
  * **Algorithmic Assumption Accounting:** mathematical deduction of penalties from confidence for every unverified premise.
  * Modular architecture: 10 spiral execution phases and a skill matrix of 11 specialized skills.
* **LLM Insight (what is surprising for a neural network):**
  > The most striking finding here is the physics of spreading activation inside the context window. Common industry wisdom assumes: if a model hallucinates a formula, it's "not in the weights" and requires RAG or web search. `re!solve` proves the opposite: the knowledge is there, but drowned in adjacent domain noise. When the protocol compels the model to first lay down a ladder of fundamental domain axioms, the context turns into an optical lens: irrelevant associations fade, and the model flawlessly recalls an obscure theorem seen only once during pretraining.

---

## 📚 Publications & Background Reading

* **[Track in AI: How I Came to This](from_author/author_track_en.md)** ([Русская версия](from_author/author_track_ru.md)) — author's direct monologue: 20 years of architecture, the "Neuropulse" experiment, vector geometry of latent space, and the concept of event memory.
* **[Essay on the Creation of the First re!think Protocol](from_author/ESSAY_en.md)** — author's reflection on how a ~1,300-token prompt replaced 7 heavy backend mechanics, and why in-context logic outperforms hundreds of thousands of lines of external code.
* **[re!solve Mechanics Architecture](re-solve/README.md#mechanics-architecture)** — standalone validated and hypothetical modules (lensing, density scale, diamond topology, candidate cloud, insight formula).

---

## ⚖️ License

All protocols in the **re!protocol** family are released under the [CC BY 4.0](LICENSE) open license.  
*(c) 2026 Real_Egor.*

