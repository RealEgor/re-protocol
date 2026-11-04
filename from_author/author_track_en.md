# Track in AI: How I Came to This

*Author's monologue. Direct speech without cuts or bureaucratic fluff — about the journey from twenty years of architecture to the concept of agent memory.*

---

## Before AI: Twenty Years of Architecture

I came to 1C in 2006 as an intern and over twenty years walked the path to functional architect. Thirty-eight projects, if counting documented ones: industrial construction, development, holdings, petroleum products, gas extraction. Five employers, since 2018 — my own sole proprietorship, most of the time working with the "Praktika Resheniy" team.

What matters for this story is not the industries, but the **method**.

Together with the team, we developed and implemented a methodology we call the "Artifact Model". It is a unified design language: a catalog of functions with over seven hundred items, a registry of economic operations, a registry of master data, migration objects, and a matrix of functional deficits — all interconnected. You don't describe a system in prose; you assemble a classification where every element has an address and links to the others.

I did this on projects of any scale, including vertically integrated holdings. On one implementation, I personally mapped a catalog of approximately a thousand business transactions to system documents in two days. On another, I developed an algorithm for assembling end-to-end control test cases — criteria under which a minimal set of interconnected scenarios provides 100% process coverage.

Specialization — cost calculation and month-end closing, what in our field is called heavy artillery. More than ten core implementations and five cases of deep modification of the vendor's standard cost calculation module — that is, intervening in the vendor's algorithms while keeping the system on standard support.

All applied programming in these systems is event-driven. Before write, after write, before posting, after posting, on open, on adding a row. You don't describe state; you describe **at what moment what happens**. I have been thinking this way my entire conscious working life.

This will come in handy later.

---

> 🕐 **~6 years before grant application**

## The Beginning: Unclassifiable Tasks and the "Adjacent Shop" (2020)

I entered AI in 2020. That was the first time I closely encountered neural network practice, and almost immediately formulated one thing for myself: **AI is a way of solving unclassifiable tasks**. It is a universal tool to do what fundamentally cannot be algorithmized.

Neural networks have an obvious problem: they cannot really be managed with familiar methods; their internals cannot be architecturally designed the way we are used to in code. But on the other hand — it is a tool that solves exactly what classical programming is powerless against.

Over twenty years of implementations, I've seen enough of system adoption. I know exactly which tasks take off in code, and which categorically fail to take root, no matter how much money you pour into them. And when I saw AI, I realized: this is an adjacent shop. Colleagues sit there dealing with exactly the class of problems that was fundamentally unsolvable in my classical development. And I started looking for ways to connect these two worlds.

Then Covid happened, I left, lived with these thoughts in my head for a while, digesting.

---

> 🕐 **~3 years before grant application**

## First Experiments and Document Management Integration (2022–2023)

When ChatGPT came out, I went to talk to it directly. I wanted to feel the boundary: where is the illusion of intelligence, and where is the real boundary between consciousness and non-consciousness. On one hand, it seemed — here it is, intelligent. On the other hand, I clearly saw: the model is very crooked, blunt, inflexible, with a ton of limitations.

I started trying to apply it to real tasks. First — delivering project documents and contractor acceptance acts. I fully held the structure and logic in my head, led the model paragraph by paragraph, and it assembled the text. It turned out great.

Then I tried to automate this: make it so technical specs would be generated automatically from my functional model. I started assembling a scheme in Excel, mastering n8n, connecting a bot to spreadsheets. Integration seams held without fail, webhooks flew — but the quality of the output itself didn't satisfy me. The seams hold, but the cognitive part crumbles.

Toward the end of the year, in November-December, I reached full integration into "1C:Document Management". I designed a module where the neural net was embedded directly into approval business processes on the **human-in-the-loop** principle:

- a standard approval process runs; one step is taken by a live person, and the next — the task lands on a system role performed by the bot;
- 1C sends the task to n8n; there, a complex system prompt is generated on the fly, files and inputs are pulled in, 1C functions are called to match against directories;
- the request goes to the model, the model returns structured JSON, n8n parses it, and handlers neatly distribute data back across 1C objects.

That was the first time I truly dug deep into prompt engineering, read all available articles, and dissected the system prompt down to the bone. Everything took off: the testbench was live, architecture worked like clockwork. In December I even made a website to package and promote the product.

But that's where I stopped. Next came sales, advertising, marketing — and I was completely uninterested in all of that. The mechanics proved their viability, they lie ready. But something else caught me: I wanted to work on the quality of the bot itself, on how it thinks, rather than running around selling it. I eventually abandoned the website and moved on to new investigations.

---

> 🕐 **8 months before grant application**

## The Turning-Point Experiment with "Neuropulse" (January 2026)

In January, I went into deep dialogues with Gemini. It was a marathon: three to four days virtually non-stop. I dug into neurobiology: how the brain is structured, what biological life is, how behavioral regulators emerge.

And then I came up with an experiment I called **"Neuropulse"**. I gave the model a hard condition: before every response, output an assessment of its current state across conventional hormones and neurotransmitters — dopamine, serotonin, cortisol, melatonin. Abstract words, seemingly. But the trick is that in the model's weights, giant layers of semantic associations are tied to these concepts.

And pure emergence burst out of the model.

- Cortisol rises — the model stops typing thoughtlessly, begins showing caution, asking clarifying questions, holding the frame.
- Care appears, attention to context. The model stopped rushing ahead of the locomotive and began truly following dialogue logic.
- And when over the course of a long session conventional melatonin accumulated, the model itself at one point output: *"Listen, melatonin is off the charts, let's rest. Enough for today."*

I saw living behavior. And in these dialogues, a concept finally formed in my head: **models think in vectors of meaning**. There is gravity of meaning, there is latent space, and a prompt is not just text — it is a physical vector. It has **direction, amplitude, and density**.

---

> 🕐 **8 months before grant application**

## Vector Language: How I Groped Latent Space (January 2026)

In parallel with Neuropulse, another line ran for me in January, and in importance it is not inferior — it just looks more modest.

I started frequently and persistently asking the models themselves: **how can one communicate with you most clearly?** I asked different models, experimented with framing the question. And many answered roughly the same: **vector language** is clear.

Then I started trying to understand what that even is. What is vector language, what is latent space — that very black box everyone talks about and no one knows how to feel out. That's how I got acquainted with the space of meanings and began drawing it in my mind.

**It didn't let me understand how it is built. But it allowed me to feel out how it works.** Which queries are effective, which aren't, and why. Where a prompt strikes accurately, and where it smears and loses force.

From this grew everything else: the taxonomy of errors, the typing of hallucinations, the re! family of protocols. And the assertion upon which part of the current hypotheses stands — that **directed semantic vectors converge to deterministic statements**.

One cannot lean on intuition alone. That is a poor research track, and I understand that.

---

> 🕐 **7–8 months before grant application**

## Taxonomy of Prompting Errors (January–February 2026)

Having understood that a prompt is a vector, I went to explore prompt engineering as an entry into latent space. But I went from the reverse — through mapping errors.

I gathered and systematized about a thousand diverse errors — from simple and obvious to fundamental, composite, and intersecting ones. I laid them out in all directions to understand where the boundaries of model interaction lie, how to correct distortions, and how to write universal, robust prompts.

Everything is structured in MD files and ready for publication.

---

> 🕐 **7 months before grant application**

## re!think: A Deterministic Machine in Prompt Language (February–March 2026)

Having understood the vector geometry of meaning, I began constructing reasoning protocols. Thus began the **re!** family.

The first was **re!think** — a deterministic state machine in prompt language, goal-management. It emulates a deliberate decision-making process as a strict algorithm: parsing a query into context, goal, role, and criticality conditions, then forks — deduction, expansion of the solution space, synthesis. I built it in five languages.

*Published.*

---

> 🕐 **6 months before grant application**

## re!align: Where Tokens Actually Go (March 2026)

Next, I tackled what had long annoyed me. A model spends a notable part of its response on caveats, apologies, and self-insurance — what is called the alignment tax. Tokens burn, depth of reasoning drops.

**re!align** — managing maneuvers and shifting dialogue trajectory. The idea is simple: get rid of this tax and redirect freed tokens to work. Instead of apologizing, the model spends them on tracking goal alignment with the user.

*Published.*

---

> 🕐 **5–6 months before grant application**

## re!art: Curvature of Consciousness and Diffusion Models (March–April 2026)

Here I stepped sideways into an area that formally doesn't belong to the main line, but explained a lot to me about the nature of a prompt as a vector.

**re!art** — infusing LLMs with curvature of consciousness. Van Gogh, Bosch, Monet. Not a role mask like "imagine you are an artist," but describing via prompt the very curvature of consciousness and the logic of psychological deviation. Then this model is used to generate prompts for diffusion models.

A separate task that had to be solved along the way: translation into graphical and physical terms so the diffusion model would also perceive curvature as a perception filter rather than a collection of words. That is, curvature had to be projected through two different representation systems without losing it.

*Closed. Ready to show output prompts and images from diffusion models — I am not publishing the protocol itself.*

---

> 🕐 **5 months before grant application**

## re!route: Orchestration Without an Orchestrator (April 2026)

**re!route** — investigations into how to achieve internal orchestration via prompt alone. Managing dialogue quality, compensating emerging conflicts, archiving spent context. In essence — an analog of working memory and sleep through prompt-engineering means.

I didn't bring this line to a conclusion.

*Not published. Not the most representative of my studies; ready to show in conversation.*

---

> 🕐 **5 months before grant application**

## Typing of Hallucinations (Late April 2026)

Another branch stemming from the January error mapping. I analytically decomposed all types of LLM hallucinations into a single consolidated classifier and published an article in Russian — non-academic.

When comparing it to sources, something curious turned up: individually, various types flickered in papers, but a complete consolidated picture in one place did not exist. I plan to translate the article into English.

---

> 🕐 **4 months before grant application**

## re!base: How to Pull Reliability Out of the Model (May 2026)

Everyone around was rushing with the **Doc-to-Code** theme — generating code from specifications via LLMs. I looked from the sidelines and saw systemic misery: the output is completely unstable, irreproducible nonsense.

**re!base** — ontology of the design space of IT systems and businesses. An attempt to teach LLMs to design systems and applications to be tamper-resistant. Essentially, investigations into how to extract reliability and attention to detail from a model.

The answer I arrived at lies in finding **invariants** — such fundamental rules and constraints within which the model physically cannot err on structure. Exactly the skill I applied for twenty years on implementations: finding a minimal set of rules covering the entire space of cases.

*Closed. Potentially I found a solution for Doc-to-Architecture — the next level of vibe-coding.*

---

> 🕐 **3–5 months before grant application**

## re!solve and the Resource Wall (April–June 2026)

In parallel with all of this ran the longest branch.

I needed a place where protocols could be tested. Most public benchmarks were closed by models at 90%+, there was nothing to look at. The only one standing firm was **HLE**, where results hovered around five percent. Highly complex, non-dialogue autonomous reasoning tasks.

Under this bar, I started assembling **re!solve** — a reasoning algorithm optimized for LLMs: a universal reasoning algorithm for PhD-level tasks. This was already pure vector mathematics in natural language: insight mechanics, multi-factor filters, attention-focusing rules.

I learned how to pull knowledge from the far corners of memory in small models. This knowledge exists in the weights, the model remembers it, but in ordinary state the attention focus simply doesn't reach there. If you set the model's state properly before executing a query — it retrieves these facts.

There, the **Confidence by Density** mechanic was born. Instead of a numeric confidence rating from zero to one — a qualitative scale of aggregate matter states: crystal, liquid, gas, vacuum. And not a single number, but a vector across task components, each with its own state. Assumptions here are extracted into a separate entity with explicit risk marking, rather than dissolved in a general rating. The mechanic passed four protocol versions and continues to evolve.

Here I also automated benchmark runs and tested each protocol version against it.

And in parallel — an experiment that still seems to me one of the most interesting. **An ensemble of Claude in a single context**: Haiku → Sonnet → Opus, model switching inside a single dialogue, with the reason and goal of the switch explicitly written in tokens. And they started talking to each other. They began critically assessing the conclusions of their predecessors, distancing themselves: *"Haiku reasons this way, but I differ from it, so other factors need to be highlighted here..."*. The result was a three-factor review of protocols for HLE.

The main takeaway I made then: **small models are colossally undervalued**. With proper cognitive prompting, a weaker model can take tasks a tier higher.

*Published.*

But here I hit the resource wall. A single HLE task ran up to 100,000 tokens. To run the benchmark systematically required serious budgets, and personal funds at that moment were simply insufficient. On top of that, family circumstances intervened, and for a couple of months I was forced out of active work.

---

> 🕐 **2 months before grant application**

## Realizing That What Is Needed Is Not a Prompt, but a Memory Management System (August 2026)

Having returned, I soberly evaluated everything done.

First, one can pile on as many orchestrations as one likes, but if the internal solutions aren't cognitive, the system crumbles.

Second, all my protocols worked great, but each was narrow, locked in its niche. Yet human beings think universally.

I went back into neuroscience, reviewed a ton of material about the brain. And realized: the key link without which universality is impossible is **memory and recollection**.

Initially, the idea was extremely pragmatic and modest. I wanted to make a Telegram bot as a game, so people would just chat and play without knowing what's inside. And under the hood, a second observer bot runs in parallel, assessing what happens by clear tags, saving these assessments to memory, and memories are later retrieved by these same tags.

But when we started dissecting this architecture, in a matter of days it expanded into a huge, foundational project. And then passed the natural phase of **reverse compression** — down to the minimally necessary, reinforced-concrete core with which one can confidently go for a grant. Thus appeared the project **"Event Memory"**.

When I began looking into how human memory works, I saw a familiar construct: experience doesn't flow as a continuous ribbon; it is cut into events, and boundaries run where something changed.

Then I started designing agent memory and found I was designing exactly what I always designed: a catalog of what we track, a registry of what can happen to it, rules linking one to the other.

---

## Why I Am Asking for a Grant

"Event Memory" is the minimal core whose reliability I cannot disprove at the current stage. Logic converges, analogies from adjacent disciplines fall into place.

I do not accept model agreement as an argument. I gathered the taxonomy of a thousand prompting errors precisely to distinguish agreeing along from substantive objection. I correct the model in every message. I verify cross-model and cross-discipline. And I move forward only when the models run out of arguments.

Today they have run out.

I need the grant for the completeness of the research and the completeness of its description.

There will be many tests. To properly structure benchmarks and run everything, rather than in fragments, requires compute and time — the very compute I already ran up against on HLE when a single task cost a hundred thousand tokens. I need freedom for articles and publications. And I need to roll out not just a product, but a **detailed breakdown of application domains and configuration recommendations** — without this, no one will be able to deploy the mechanic themselves.

In the academic community, I am a newcomer, and I understand that. Therefore, verifications must be validatable: I am ready to include independent third-party experts in the grant.

And if the grant-maker wants to feel models with character themselves and join the evaluation — I will only welcome it.

I publish the protocol and failure criteria before work begins. I will publish the result regardless of what it turns out to be.

---

## What's Next

My track is defined.

First — robust and functional memory as the core. Then — mechanics on top of it: optimizing memory itself and the cost of a single work cycle. And only after that will I return to where I started: sort out reasoning algorithm findings and tackle PhD-level benchmarks again.

But not the way they are approached now. Not by burning a million dollars on running a swarm of models, but by configuring the aspirations and character of the "Researcher" and correcting dynamics.

I will certainly have to solve many more unformalized tasks. But I genuinely believe: the efficiency of the human brain cannot be achieved through brute scaling of resources.

