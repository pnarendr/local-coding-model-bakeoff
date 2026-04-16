# Local Coding Model Bakeoff: Dense vs MoE for Practical Development

*An evidence-based comparison of **Qwen3.5-27B (Dense)**, **Gemma 4 31B (Dense)**, **Qwen3.5-30B-A3B (MoE)**, and **Gemma 4 27B (MoE)** across a small custom harnessed-agent benchmark suite*

**Author:** Pat Narendra PhD  
**Date:** April 15, 2026

---

## Executive summary

I ran a small but diverse benchmark suite to compare four local coding models as practical development assistants:

- **Qwen3.5-27B (Dense)**
- **Gemma 4 31B (Dense)**
- **Qwen3.5-30B-A3B (MoE)**
- **Gemma 4 27B (MoE)**

The goal was not to produce a universal leaderboard. The goal was more practical:

> **Which local model should I trust most for coding when I need an alternative to frontier tools like Codex?**

### Headline conclusions

- **Dense models were more trustworthy than MoE models** across the evals.
- **MoE models were often faster/zippier, but less consistent on grounded debugging and minimal patching.**
- **There was no single model that cleanly won every custom task.**
- The real daily-driver contest was **Qwen3.5-27B (Dense) vs Gemma 4 31B (Dense)**.
- Based on the combination of these evals and currently available public coding benchmark evidence, **Qwen3.5-27B (Dense) is the safer daily-driver recommendation**, with **Gemma 4 31B (Dense)** as a strong second-opinion model.

### Practical recommendation

**Daily driver for local coding:** **Qwen3.5-27B (Dense)**  
**Best alternate / second opinion:** **Gemma 4 31B (Dense)**  
**Use MoE models for:** fast drafts, brainstorming, scaffolding, and supervised parallel attempts  
**Do not rely on MoE as the final authority for debugging or patch-minimality**

---

## Why this benchmark was run

Local models are getting fast enough to be useful in real development workflows, especially on:

- RTX 3090-class local servers
- MacBooks using Metal
- mixed local + remote coding setups
- harnessed agents that can read files, propose patches, and write reports

But “fast enough” is not the same as “trustworthy enough.”

For coding, especially debugging, I care about:

- correct bug localization
- groundedness to the actual repo artifacts
- smallest safe patch
- clean reasoning under ambiguity
- consistency across runs
- not drifting into plausible-but-wrong explanations

That is why I built and ran a small challenge suite rather than relying only on vibes or headline benchmarks.

---

## The benchmark suite

I used three custom tasks, each designed to probe a different dimension.

### Task 14958 — Validation / spec reasoning task
This task focused on a zip/postcode validation regression.

What it tested well:
- structured reasoning
- bug interpretation from textual/context clues
- ability to propose a focused validation fix

What it did **not** test as strongly:
- deep tool use
- repo navigation under ambiguity
- full agent loop discipline

**Role in the suite:** corroboration task

---

### Task 15241 — Non-vision visual-debugging inference task
This task simulated a frontend visual bug without requiring actual screenshot-capable vision.

What it tested:
- root-cause inference from symptoms
- CSS minimality and scoping
- hit-testing / overlap / pointer interception reasoning
- harness discipline in producing a minimal fix and verification plan

**Role in the suite:** ambiguity handling and smallest-safe-fix discipline

---

### Task 15273 — Optimistic UI / offline retry / reconciliation task
This replaced an earlier weaker challenge.

What it tested:
- multi-file causal reasoning
- state-flow debugging
- optimistic UI reconciliation
- offline retry semantics
- distinguishing root fix from downstream symptom fix

**Role in the suite:** strongest practical debugging discriminator in the final suite

---

## Important note on a discarded task

An earlier task, **15193**, was removed from the final benchmark story.

Why:
- it had been composed by a local model
- upon closer review, the challenge itself was not trustworthy enough
- the model outputs often solved a plausible but incorrect subsystem
- that made it weak as a grounded comparison point

Rather than force confidence from questionable data, I threw it out and replaced it with **15273**, which produced much more meaningful debugging evidence.

That was the right call.

---

## How the models behaved overall

### Dense vs MoE: the clearest pattern

The most stable signal across the suite was not “Qwen always wins” or “Gemma always wins.”

The most stable signal was:

> **Dense > MoE for trusted coding work**

Across families, the dense models were:
- more consistent
- less likely to drift into elaborate but wrong stories
- better at identifying the right bug mechanism
- better at delivering smaller, safer fixes

The MoE models were often:
- faster
- more fluent
- good enough for drafts and exploration
- but less dependable for exact bug localization and disciplined repair

That distinction matters more than raw speed in real development.

A fast wrong answer wastes more time than a slower correct one.

---

## Final task-by-task summary

## Task 14958 results

**What the task rewarded:** identifying a validation regression tied to postal-code regex behavior.

### Best result
**Qwen3.5-30B-A3B (MoE)** produced the strongest grounded submission on this task.

### Interpretation
This task was useful, but it was also the easiest of the three and the most vulnerable to generic reasoning.  
So I treat it as supporting evidence, not the main decider.

**Takeaway:** Qwen3.5-30B-A3B (MoE) did well here, but this task alone should not drive the final daily-driver decision.

---

## Task 15241 results

**What the task rewarded:** diagnosing a visual overlap / pointer-interception bug from text and layout clues, then proposing the smallest safe CSS fix.

### Best result
**Gemma 4 31B (Dense)** was the strongest performer on this task.

Why:
- best practical patch
- strongest minimality
- best mobile scoping
- cleanest harness-style deliverables

**Takeaway:** Gemma 4 31B (Dense) looked especially strong on ambiguity-heavy debugging that rewards conservative, production-safe patching.

---

## Task 15273 results

**What the task rewarded:** tracing an optimistic UI duplicate-comment bug across retry, reconciliation, and identity flow.

### Best result
**Qwen3.5-27B (Dense)** was the strongest performer on this task.

Why:
- best grounding to the actual artifacts
- strongest connection between patch, bug mechanism, and fix
- clearest diagnosis of identity/reconciliation failure
- strongest final recommendation among the four on the deepest debugging task in the final suite

**Takeaway:** Qwen3.5-27B (Dense) looked strongest when the task required sustained causal debugging across multiple files and state transitions.

---

## Final model readouts

## Qwen3.5-27B (Dense)

### Strengths
- strongest on the hardest final debugging task (15273)
- consistently strong analytical coverage
- best public coding-benchmark story among the candidates
- good at connecting artifact evidence to bug mechanism

### Weaknesses
- can be slightly broader than necessary in fix scope
- not always the cleanest “smallest safe patch” model

### Best role
**Primary local coding daily driver**

---

## Gemma 4 31B (Dense)

### Strengths
- strongest on 15241
- very good at disciplined patch minimality
- good practical engineering instincts
- often produces the cleaner “ship this” patch

### Weaknesses
- in this suite, sometimes slightly less grounded than Qwen3.5-27B (Dense) on the deepest debugging task
- may over-index on a containment fix instead of the most direct root fix

### Best role
**Second-opinion model and strong alternate daily driver**

---

## Qwen3.5-30B-A3B (MoE)

### Strengths
- respectable on multiple tasks
- strongest result on 14958
- surprisingly competitive on 15273

### Weaknesses
- less consistent than the dense models
- more likely to need supervision
- not as trustworthy as the dense models for final debugging authority

### Best role
**Fast draft model / supervised sidekick**

---

## Gemma 4 27B (MoE)

### Strengths
- decent practical reasoning in parts of the suite
- capable of plausible fixes

### Weaknesses
- weakest evidence base overall in the final benchmark story
- less consistent and less grounded than the dense models
- not the best choice for trusted coding work

### Best role
**Exploration / secondary candidate only**

---

## Ranking story

If you only looked at the custom tasks, you would **not** get a perfectly stable single winner across every task.

That is important.

This benchmark did **not** say:
- Gemma always wins
- or Qwen always wins

Instead, it said:

- **Gemma 4 31B (Dense)** is especially strong on conservative minimal-fix debugging
- **Qwen3.5-27B (Dense)** is especially strong on deeper multi-step causal debugging
- **Both dense models are ahead of the MoE models for trusted coding use**

That is already a meaningful result.

The tie-break then comes from external coding evidence.

---

## Why Qwen3.5-27B (Dense) gets the daily-driver recommendation

My custom tasks alone are not enough to declare a universal winner.

So I combined them with broader public coding evidence.

The currently available public picture favors **Qwen3.5-27B (Dense)** a bit more cleanly for coding-specific evaluation, especially around SWE-bench-style and tool-using code tasks.

That matters because my real decision is not “who won one homemade challenge.”

It is:

> **Which local model should I reach for when I need a practical substitute for Codex?**

On that question, the best current synthesis is:

### Recommendation
**Choose Qwen3.5-27B (Dense) as the daily driver.**  
Keep **Gemma 4 31B (Dense)** installed as the second-opinion model.

That gives the best of both worlds:
- Qwen3.5-27B (Dense) as the main local coding engine
- Gemma 4 31B (Dense) as a cross-check when the problem is ambiguous, UI-heavy, or when you want a smaller safer patch

---

## What this means for real workflows

### Suggested workflow

#### Use Qwen3.5-27B (Dense) for:
- main implementation work
- deeper debugging
- repo-level diagnosis
- multi-file bug tracing
- fallback when Codex tokens are exhausted

#### Use Gemma 4 31B (Dense) for:
- second-opinion review
- alternative patch strategy
- conservative patch proposals
- UI / frontend debugging cross-checks

#### Use MoE models for:
- brainstorming
- initial scaffolding
- parallel low-cost attempts
- generating candidate tests or rough approaches

### What not to do
Do not treat MoE outputs as automatically ready-to-merge just because they are fast and fluent.

---

## Lessons learned from the benchmark

### 1. Custom evals are useful, but dangerous if trusted too much
I found real signal, but I also found how easy it is for a weak challenge or self-evaluation to distort the picture.

### 2. Groundedness matters more than polish
A detailed answer is not necessarily a correct answer.

### 3. Dense models currently earn trust better than MoE models in coding use
This was the most stable pattern in the results.

### 4. Smallest-safe-fix discipline is a useful discriminator
Models that can make narrow, correct changes are more valuable than models that produce elaborate but broad fixes.

### 5. The best benchmark story mixes custom tasks with public evidence
That is what turned an ambiguous “Gemma or Qwen?” comparison into a usable recommendation.

---

## Final recommendation

If I had to pick **one local model** today as a practical daily coding fallback when frontier tool usage is constrained:

# **Qwen3.5-27B (Dense)**

If I could pick **two**:

1. **Qwen3.5-27B (Dense)** — daily driver  
2. **Gemma 4 31B (Dense)** — second-opinion / alternate patching style

If I had to summarize the dense-vs-MoE result in one sentence:

> **For coding, dense models are currently the safer bet; MoE models are appealing for speed, but not yet the same trust tier.**

---

## Suggested next step

To make this stronger and more public-benchmark-like, the next phase should add a **small fixed SWE-bench-style slice** on top of this custom suite. That would make the daily-driver recommendation even more defensible.

But even now, the practical choice is clear enough to act on:

**Run Qwen3.5-27B (Dense) as the default local coding model. Keep Gemma 4 31B (Dense) as your backup and verifier.**

---

## Shareable short version

I compared **Qwen3.5-27B (Dense)**, **Gemma 4 31B (Dense)**, **Qwen3.5-30B-A3B (MoE)**, and **Gemma 4 27B (MoE)** across a small benchmark suite focused on validation bugs, frontend debugging, and optimistic UI reconciliation. The strongest stable pattern was not one family always beating the other — it was that **dense models consistently outperformed MoE models on trustworthiness for coding tasks**. **Gemma 4 31B (Dense)** was especially strong on minimal, production-safe fixes, while **Qwen3.5-27B (Dense)** was strongest on the deepest debugging task and currently has the better public coding-benchmark case. My current recommendation is **Qwen3.5-27B (Dense) as the daily driver**, with **Gemma 4 31B (Dense)** as the best second-opinion model.
