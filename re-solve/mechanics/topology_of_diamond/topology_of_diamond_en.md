# Diamond Face Classifier & Knowledge Topologies
# re-solve v4.1 | Developer Reference (EN)
# (c) 2026 Real_Egor. Licensed under CC BY 4.0
# Source: https://github.com/RealEgor/re-protocol

---

## Purpose of this document

This file is a **protocol developer reference**, not part of any executable prompt.

It contains the full two-layer classifier from which the v4.1 patch was derived
(face f + position verdict for b and e). The full classifier is too heavy for
model working memory, but is necessary for:
- protocol auditing and discovery of blind spots
- development of future versions (v5+)
- understanding why a specific patch was selected over alternatives

---

## LAYER 1 — Face Classifier

Each face is a **question-attack**. Postulates are stated as assertions;
during cutting the model inverts them into an attack question and states a verdict.

**Variables:** `c` = Context, `g` = Goal, `t` = Tool, `v` = Verification.
`[!]` = topological modifier — the variable's behavior differs, see annotation.

---

### [CORE] — What is this?

```
c → The task uses the concept in the same scope in which it is defined
g → The goal requires exactly this concept, not an adjacent one with a similar name
t → The method operates on entities of exactly this type
v → This is the canonical formulation — not reconstructed, not blended
```

---

### [DOM] — Where does it live?

```
c → The task's domain is inside the Brilliant's applicability space
g → The goal requires an answer in exactly this coordinate system
t → The method operates in the same domain — imports no foreign assumptions
v → Verification uses instruments valid in this domain
```

---

### [MECH] — How does it work?

```
c → All input variables required by the mechanism are present in the context
g → The mechanism's output matches what the goal requires — in type and direction
t → The method uses the mechanism correctly: no skipped steps, no inversion
v → Every step is independently reproducible and verifiable
```

---

### [AUTH] — Why do we believe this?

```
c → The epistemological status of the source is compatible with the task's paradigm
g → The Brilliant's level of evidence is sufficient for the goal
t → The method accepts this type of grounding as a valid input
v → The assertion traces back to the primary source without gaps

[!] Axiom:      v = "cannot be verified from inside the system — only explicitly postulated"
[!] Hypothesis: v = "distinguishable from competitors by its predictions"
```

---

### [CONTR] — What is this NOT?

```
c → The context contains no twin concept masquerading as the Brilliant
g → The goal cannot be equally closed by the twin
t → The method has sufficient resolution to distinguish the Brilliant from its twin
v → The twin does not produce the same answer — a working elimination tool
```

*Note:* This postulate was introduced into the protocol as face **(f)**
"nearest conceptual twin".

---

### [LIM] — When does it break?

```
c → The task's parameters are inside the safe operating zone — not at or beyond the boundary
g → The goal does not require an answer precisely at the break point
t → The method is stable near the Brilliant's boundary conditions
v → The break boundary = primary verification handle; stress-test goes here

[!] Heuristic: v = "known failure zones are the primary verification test"
```

*Note:* The `c` postulate of this face was introduced into the protocol as the
mandatory position verdict for faces **(b)** and **(e)**.

---

### [IMPL] — What follows from this?

```
c → The implications do not contradict known facts in the context
g → The goal requires the Brilliant's implications, not the Brilliant itself (or vice versa)
t → The method can propagate implications of this Brilliant's type
v → Implications are verified against independent data — not against the same Brilliant
```

---

### [COST] — What do we pay for this?

```
c → The cost of application creates no forbidden side effects
g → The price of application does not negate the value of reaching the goal
t → The method's overhead is justified by the quality of the result
v → —
```

---

### [CTRF] — What collapses without this?

```
c → Without the Brilliant, part of the context becomes inexplicable
g → Without the Brilliant, the path to the goal breaks — it is load-bearing, not decorative
t → The method requires exactly this Brilliant as input
v → Negating the Brilliant leads to contradiction with logged facts (reductio)
```

---

### [VER] — How to verify this quickly?

```
c → The context contains an independent data point for a checksum
g → The invariant's format is compatible with the expected answer format
t → The method can generate this type of verification invariant
v → The verifier is independent of synthesis — not the output of the same process
```

---

## LAYER 2 — Topologies: active-link formulas

**Notation:**
- `★` = all four variables active (c, g, t, v)
- `(x,y)` = only the listed variables are active
- `!` = topological modifier (see `[!]` annotations in Layer 1)
- Absent variable = not relevant for this topology

---

```
[1  DEFINITION / CONSTRUCT]
    CORE★ · CONTR★ · LIM★ · AUTH(c,v)

[2  AXIOM / POSTULATE]
    DOM★ · AUTH(c,t,v!) · CONTR(c,g,t) · LIM(c,v) · CTRF(v)

[3  THEOREM / LOGICAL LAW]
    MECH★ · LIM★ · AUTH(c,v) · CONTR(c,v) · DOM(c,g)

[4  SINGLE FACT / EMPIRICAL CONSTANT]
    CORE★ · AUTH(c,t,v) · LIM(c,v) · CONTR(c,g,v)

[5  MECHANISM / STRUCTURAL REACTION]
    MECH★ · CONTR★ · LIM(c,t,v) · DOM(c,t) · IMPL(c,v)

[6  WORKING HYPOTHESIS / PROBABILISTIC MODEL]
    AUTH★ · LIM(c,v!) · CONTR(c,g,v) · IMPL(c,v) · COST(g,t)

[7  STATISTICAL TREND / CORRELATION]
    CONTR★ · AUTH(c,t,v) · DOM(c,g) · LIM(c,v)

[8  SYNDROME / DIAGNOSTIC PATTERN]
    CORE★ · CONTR(c,g,v) · MECH(t,v) · LIM(c,v)

[9  CAUSAL GRAPH / HISTORICAL NARRATIVE]
    MECH(c,t,v) · AUTH(c,v) · LIM(c,v) · CONTR(c,v)
    ⚠ not a single Brilliant — cut each link independently

[10 DETERMINISTIC ALGORITHM]
    MECH★ · LIM(c,v) · DOM(c,t) · AUTH(v)

[11 HEURISTIC / OPTIMAL STRATEGY]
    LIM★(!) · COST(g,t) · CONTR(c,g,v) · DOM(c) · AUTH(c)

[12 NORM / PRECEDENT]
    DOM★ · AUTH(c,t) · LIM(c,v) · CONTR(c,v)

[13 INTERPRETATION / SYMBOLIC CONSTRUCT]
    AUTH(c,g,t) · CONTR(c,v) · LIM(c,v) · DOM(c) · COST(g)

[14 DYNAMIC INTERACTION / EQUILIBRIUM]
    MECH★ · CONTR★ · LIM(c,v) · IMPL(c,v)

[15 ANALOGY / STRUCTURAL ISOMORPHISM]
    DOM★ · LIM(c,g,v) · CONTR(c,v) · MECH(c,t)
```

---

## How to use during cutting (flow)

```
Crystal found
  → identify topology (row from Layer 2)
  → for each active link, find the postulate in Layer 1
  → Domain view: what the face says inside the Crystal's native theory
  → Task view: invert postulate into question-attack → verdict match / mismatch / fragility
```

**Example (Topology 3 — Theorem, face [LIM·c]):**
```
Domain view: Bernoulli's theorem applies under incompressible flow (Re < 2300).
Task view:   task operates with compressible gas at M > 0.3 → incompressibility fails.
Verdict:     MISMATCH → fragility(b),(e)
Action:      patch [A] required, or replace topology with [5 MECHANISM].
```
