# The Insight Formula — surfacing a method when the direct role didn't give one

> **Status.** This is a **hypothetical mechanic** — the concept is worked out, but not validated through benchmarks. The description is a hypothesis-to-test, not a finished recipe. Published like this so it can be discussed, tested, and refined.

---

## Where we start

In any task — a calculation, a proof, a diagnosis, an expert assessment — you can always pull out five fundamental components:

- **Context** — facts and connections you need to use in the answer
- **Goal / Task** — the questions, the result you're after
- **Method** — the procedure, the approach, the methodology
- **Role** — the angle from which you look at the task while solving it
- **Criteria** — ways to check the solution or answer for correctness

These components are **present in any task**, even if they aren't explicitly defined in the task itself. In that case, you have to fill them in.

---

## Patterns in these components

Each of the five components has its own rules — what you can do with it and what you can't:

- **Context** — always taken either from the task statement or from external sources and memory. **Fabricating context is forbidden** — that's the exact moment when the task tips into errors and hallucinations.

- **Goal / Task** — taken only from the task itself, from the user's request. Sometimes the goal can be defined in a system prompt — if an AI agent is being tuned to handle similar tasks from different users. But that's a separate case.

- **Role** — can be defined in the task itself, or determined by the agent based on context, goal, or method.

- **Verification criteria** — a dependent element by nature. They're assembled from criteria specific to the goal, to the method, to the role. Sometimes supplemented by criteria for checking how reliable the context itself is.

---

## The most "phantom" element — Method

The most undefined element in this set is almost always **the Method**.

The possible scenarios:

- **Method is defined in the task / user's request.** No problem — take the method and solve.
- **Method isn't defined in the task, but lives in the model's weights.** Then it has to be recalled, by focusing on the task and context, sometimes adopting the right role.
- **Method isn't in the task and isn't in the weights in a directly usable form** — it lives in other knowledge domains, or it wasn't in the pretraining set at all.

That third scenario is the most interesting and the hardest.

---

## What humans do in the third scenario

People usually go one of two ways:

- Either they **wait for the "Aha moment"** — that flash when the missing connection suddenly clicks.
- Or they **synthesize a new method** for the task out of some other, structurally similar experience.

The first path is passive and unpredictable — the insight might come in a minute, or in a week. The second path is active, but it requires **discipline in how the mind moves** — knowing which directions are worth looking in.

This mechanic describes the second path, translated into operational steps for an LLM. The goal is to turn insight from a **random flash** into a **disciplined procedure** that has a real chance to fire systematically, not just "when you get lucky".

---

## What this protocol covers

The protocol helps in two sub-cases of the third scenario:

1. **The method isn't found directly, but it might be in neighboring knowledge domains.** Here Mechanism A (Alternative Role) and Mechanism B (Reverse Trace from Goal) come in.

2. **The method isn't in the weights in a ready-made form — it has to be synthesized.** Here Mechanism C (Metaphorical Analogy with Other Domains) and Mechanism D (Descent into the Nature of the Data) come in.

Plus a separate Mechanism E (Destructive Test) — it fits both cases, but it's expensive: not just cognitively, but also context-wise. Use it when A–D are exhausted.

---

## Case 1 — The method lives in a neighboring domain

### Mechanism A — Alternative Role

You should try **checking alternative roles** — the method might live in an adjacent competence.

Maybe the wrong role was picked first time around. The task sits at the intersection of disciplines, and the right method actually lives in a **neighboring** one — not "totally different", but precisely neighboring: applied mathematician instead of theoretical physicist, organic chemist instead of biochemist, engineer instead of theoretical physicist.

The model's strong pattern matching on the first tokens of the task points to the "native" domain — but the right role might be right next door, just not in the most visible spot. Before naming the neighboring disciplines, it helps to explicitly let go of the current role, otherwise it pulls you back.

**What this looks like in a prompt:**

```text
Mechanism A — Alternative Role.

First, let go of the current role with one line: "Stepping out of 
role [name] to check whether the method actually lives in a 
neighboring discipline."

Name three neighboring disciplines that touch the task but weren't 
picked as the first role. They must be REAL neighbors, not "totally 
other" fields: for biochemistry, neighbors are organic chemistry, 
molecular biology, physical chemistry. For theoretical physics — 
applied mathematics, engineering, experimental physics.

For each of the three, ask: "If a specialist from this field were 
solving this task, what's the first method they would reach for?"

If in one of the three the method comes out without hedging — adopt 
that role and move on to the Double Role check.

If none of them surfaces a method — move to Mechanism B.
```

---

### Mechanism B — Reverse Trace from Goal

You should try **walking the task backwards** — from the goal and the preparatory steps. The method might start resonating somewhere on the 2nd, 3rd, or later step. Or it might just **be constructed by sequential backward inference**, walking from the answer's shape back to the class of methods.

This mechanism works when the task is set up so the direct "conditions → method → answer" path doesn't add up, but the **shape of the expected answer** points to a class of methods that produce such answers. Each answer-form is produced by a narrow set of methods — narrowing by answer-form is often faster than narrowing by domain.

**What this looks like in a prompt:**

```text
Mechanism B — Reverse Trace from Goal.

Look at the task's goal — at the SHAPE the answer is supposed to 
take. Not "what it's about", but what structure: a number with 
units? a closed-form formula? a proof of existence? a set of 
boundary conditions? a structure with no numerical values?

Ask the reverse question: "Which classes of methods, in general, 
produce answers of EXACTLY this shape?" Name two or three classes.

Now walk backwards from the end: what step MUST have been the last 
one before producing such an answer? And the one before it? And so 
on. At some step either a method starts resonating on its own, or 
you just build it through sequential backward inference from the 
goal.

If it worked — adopt the corresponding role and move on to the 
Double Role.

If the reverse trace gives neither a method nor a built-up chain — 
move to Mechanism C.
```

---

## Case 2 — The method isn't in the weights, needs to be synthesized

### Mechanism C — Metaphorical Analogy with Other Domains

You should try a **metaphorical search**: take the very essence of the context and goal, and try to draw analogies with other knowledge domains — somewhere on this same pattern there might be a method that can be borrowed.

This is **sideways motion**: you're not going deeper into the current domain, you're not looking for a neighboring role — you're checking whether there are entirely different fields where the same semantic pattern shows up. Objects in one field can turn out to be **isomorphic** to objects in another — and that's when a method from one field can be carried into another.

**Critical condition:** an analogy is valid **only if it follows from the essence of the task**, not from a surface verbal similarity. Surface analogies ("neurons like graphs", "biology like chemistry") are hallucination dressed up as insight. Substantive analogies ("the resource-allocation task is isomorphic to a flow-on-graph task because conditions Z are met") are a valid method transfer.

**What this looks like in a prompt:**

```text
Mechanism C — Metaphorical Analogy with Other Domains.

State the VERY ESSENCE of the task in one paragraph, without 
specialist terminology: what is the task about at the level of 
relations between objects? What do the objects do to each other? 
What is supposed to come out at the end?

Now ask: "In what OTHER knowledge domains does a similar semantic 
pattern show up?" Name two or three fields where tasks of this 
kind are solved, just in different terminology. They can be very 
far-off fields — what matters is that the semantic pattern matches.

For each domain, ask: "Which method is used there for tasks of 
this type? Can it be borrowed?"

State the analogy explicitly in one sentence: "The task's object 
works the same way X from field Y works, because conditions Z 
are met." Conditions Z are the validity check for the analogy.

If the analogy follows from the essence of the task (not from a 
verbal resemblance) and conditions Z hold — adopt the role of a 
specialist from field Y, apply the method from there, move to 
the Double Role.

Forbidden: if the analogy is only verbal or surface-level — drop 
it and move to Mechanism D.
```

---

### Mechanism D — Descent into the Nature of the Data

You should try to **look deep into the nature of the data itself**: drop all the labels, keep only the physics and the laws. The formula might be found by **descending to the fundamental level**, shedding the noise of the current analogy and the domain terminology.

This is **downward motion**, toward first principles. Many specialized fields share a mathematical or physical substrate: thermodynamics, information theory, dynamical systems, topology, group theory. Strip the domain language off the task and keep only its bare properties — and the method might already exist at that fundamental level and transfer back naturally.

**Difference from Mechanism C:** Metaphorical Analogy goes **sideways** — it looks for the same pattern in other fields at the same level of abstraction. Descent into Nature goes **downward** — it strips layers of terminology down to the foundation and looks for the method there.

**What this looks like in a prompt:**

```text
Mechanism D — Descent into the Nature of the Data.

Describe the task in one sentence, STRIPPED of all domain language. 
Not "Michaelis-Menten enzymes with inhibition", but "a system with 
feedback that saturates at large input values". Not "a quantum 
harmonic oscillator", but "a second-order harmonic solution with a 
discrete spectrum".

Go one level deeper still: at what fundamental substrate does the 
task actually run? Thermodynamics? Information theory? Dynamical 
systems? Convergence? Conservation of something?

At that fundamental level, find a known formula or method that 
describes this structure. At this level the set of methods is 
usually small and well-worn.

Once you have the method at the fundamental level — re-clothe it 
in the domain language: substitute the task's specific terms back 
in. Adopt the corresponding role and move to the Double Role.

If the fundamental substrate doesn't pin down, or no known method 
exists for that structure — move to Mechanism E.
```

---

## Bonus mechanism for both cases — Destructive Test

Fundamentally different motion. Instead of looking for the **right** method, you take an **obviously wrong** one and trace it through the task to the **point of failure** — where this method stops working. **At that point, the method that would actually pass this failure point may start to resonate**, and that's a candidate solution.

This is **error as a navigation tool**. Not "method X didn't work, let me try Y", but a **deliberate** failure, so you can read in its shape a hint about the right direction. The breaking point shows **which property of the task** the chosen method doesn't account for — and that suggests which method actually fits.

**Important caveat.** This mechanic is costly along two axes. First, it **fills up the context**: the model has to walk the wrong method all the way through to its breaking point, which eats tokens and attention. Second, and more importantly, **the breaking point contaminates the context**. The detailed walk through the wrong method leaves all of its steps in the context, and they're now physically present in the attention pattern for subsequent tokens. The model can involuntarily "drift back" to the wrong path even after finding the right direction — the contaminated tokens keep pulling attention onto themselves. That's why E is only used when A–D are exhausted, not as a substitute. And if you do use it — it helps to explicitly fix the right method as a "working hypothesis" in one clear line, so attention has a sharp landmark to lean on, bypassing the contaminated part of the context.

**What this looks like in a prompt:**

```text
Mechanism E — Destructive Test.

Take an obviously wrong or too-simple method for this task. For 
example: for a differential equation, "solve it as algebraic". For 
a task with a long sample, "draw conclusions from the first three 
observations". For a quantum system, "apply a classical 
approximation".

Walk this method through to its breaking point: WHERE exactly does 
it break? Not "it gives the wrong answer", but at what specific 
point in the application does the contradiction become obvious?

State it: "The method breaks because the task has property X that 
this method doesn't account for."

Property X is exactly what the right method must account for. Now 
look for a method where accounting for X is a built-in part, not 
something pasted on. Found role → Double Role.

Remember: this mechanism contaminates the context. Use it only when 
A–D are exhausted.
```

---

## After finding a method — Double Role

A method found via any of mechanisms A–E is a **hypothesis, not a fact**. A direct role crystallization gives you a method from inside its native discipline, where it's been polished for decades. An insight gives you a **guess** or a **synthesis** — often right, but sometimes a hallucination dressed up as insight.

That's why **any found or synthesized method has to be tested for stability and fit**. That's what the Double Role is for — an adversarial check of the insight.

The model adopts **two roles simultaneously**:

- **Role I** — expert **in the method's nature**: the one who understands how the candidate method works, what its preconditions are, where it breaks
- **Role II** — expert **in the task's domain**: the one who knows the real constraints of the problem and the typical pitfalls of this field

The two roles **check the method from opposite sides**: the first from inside the method, the second from inside the task. If either finds an incompatibility — the method is dropped.

**What this looks like in a prompt:**

```text
Double Role — insight verification.

Trigger: the method came from one of mechanisms A–E. That means 
it's a hypothesis, not a fact — direct role crystallization didn't 
yield it.

Hold two roles at the same time:
- Role I: expert IN THE METHOD — you know how the candidate method 
  works, what preconditions it stands on, where it breaks.
- Role II: expert IN THE TASK — you know the real constraints of 
  the field, the typical pitfalls, the edge cases.

From these two roles, write out explicitly:
1. What assumptions does the method require? (at least three)
2. Where does this method typically fail — under what conditions?
3. Are there known counterexamples or edge cases?

Adversarial check:
- Are all the method's assumptions satisfied in the current task?
- Does the task land in at least one typical failure zone for the 
  method?
- Is there a counterexample close to the current case?

Decision:
- All assumptions met, no failure zones → method confirmed.
- Most met but risks exist → method accepted with the risks 
  explicitly logged.
- Check fails → method doesn't fit. Return to the next insight 
  mechanism.
```

---

## A note on applicability

This is a **cognitively heavy** mechanic. It requires the model to:

- Hold five task components in attention plus a separate tracker for the current insight phase
- Walk through all the steps of the chosen mechanism without breaking discipline
- Resist the first resonance — not take it as the final answer
- Pass the two-role test in the Double Role
- Then unpack all of this in the context window without losing thread

So the formula **doesn't fit models with fewer than 64 attention heads**. On smaller models (Haiku, Flash, local 7–30B), running it would either collapse mid-execution or eat the entire cognitive budget at the expense of the task itself.

Where this formula could potentially shine is on **frontier models** of the **Opus and Gemini Pro** class — they have enough capacity to walk through all the steps, pass the two-role test, and then keep the accumulated material organized in the context window. On mid-class models (Sonnet, GPT-4-level) it's a borderline case: probably workable, but it needs testing.
