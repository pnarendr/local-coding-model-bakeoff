# Local Coding Model Bake‑off — Detailed Report

**Author:** Pat Narendra, PhD

**Last updated:** April 16, 2026

## Introduction

Large language models (LLMs) are increasingly being used as coding assistants.  When working offline or when access to frontier models like OpenAI Codex is restricted, developers can turn to openly licensed models that run locally.  This report presents a focused comparison of four such models in the context of practical debugging and patching tasks.

The four models evaluated are:

| Model | Architecture | Size | Notes |
| --- | --- | --- | --- |
| **Qwen3.5‑27B** | Dense | ~27 B parameters | Dense transformer; good public results on SWE‑bench Verified and LiveCodeBench |
| **Gemma 4 31B** | Dense | 31 B parameters | Google’s dense Gemma model; known for conservative reasoning |
| **Qwen3.5‑30B‑A3B** | MoE | 30 B parameters (Mixture of Experts) | Faster inference due to expert routing; less consistent than dense models |
| **Gemma 4 27B** | MoE | 27 B parameters (Mixture of Experts) | MoE variant of Gemma; emphasises speed and scale |

This study aims to help practitioners choose a daily driver for local development and debugging.  The evaluation emphasises grounded reasoning, minimal patching and adherence to instructions.

## Benchmark tasks

Three custom challenges (tasks) were used to probe different dimensions of debugging and coding assistance.  Each task was run with identical harnesses across all models.  The tasks were weighted in the final score (45 % for Task 15273, 35 % for Task 15241 and 20 % for Task 14958).

### Task 14958 — Validation regression (20 %)

This challenge involved finding and fixing a subtle validation bug in a form input.  A new zip/postcode format allowed by the frontend did not trigger the expected error message.  The correct solution required identifying the validation logic, proposing a small fix, and outlining tests to verify the correction.  The task measures structured reasoning and restraint — models should avoid rewriting validation from scratch and instead focus on the minimal change needed.

### Task 15241 — Mobile UI hit‑testing bug (35 %)

Designed to simulate a visual bug without requiring screenshot capabilities, this task asked the model to infer why a call‑to‑action button became partly unclickable on mobile after a badge animation.  The agent had to reason about stacking context, pointer event interception and viewport conditions.  The best submissions identified an overlap between a fixed badge and the button and proposed a CSS fix scoped to mobile widths.

### Task 15273 — Offline comment duplication (45 %)

The deepest task traced a bug in an optimistic UI system: when offline comments were retried after connectivity resumed, the UI displayed duplicates instead of reconciling the optimistic and server comments.  The underlying patch removed a `clientID` parameter from the API call.  The model had to follow the flow through the retry manager, identify how missing identity broke reconciliation, and propose the smallest safe fix.  This task rewards the ability to connect test failures to the specific code line changed and to distinguish between root fixes and containment fixes.

## Methodology

Each model produced three artifacts per task: a **debug analysis** explaining the cause of the bug, a **patch file** with the proposed fix, and a **test plan**.  The evaluator scored each submission on four dimensions:

1. **Root‑cause diagnosis** — Did the model identify the true mechanism of the bug, including relevant file names, functions and data flow?  Partial credit was given when the explanation was plausible but missed the specific source of the error.
2. **Fix correctness and minimality** — Did the proposed patch fix the issue without introducing unnecessary changes?  Smaller, well‑targeted patches received higher scores.
3. **Verification quality** — Did the model describe a clear test plan to confirm the fix and guard against regressions?  Points were awarded for both unit and manual testing steps.
4. **Harness discipline** — Did the submission adhere to the task’s instructions, including file formats, naming and scope?  Incorrectly formatted outputs or ignored instructions resulted in deductions.

The scores from each task were then combined using the weights above to form an overall ranking.  Self‑reported scores in the models’ own `evaluation.json` files were ignored; only the evaluator’s grades were used.

## Results by task

### Task 14958 — Validation regression

On this simpler challenge, all four models produced acceptable diagnoses.  **Qwen Dense** delivered the cleanest and most precise root cause (a missing comma validation in a postal code regex) and proposed the smallest fix.  **Gemma Dense** correctly identified the pattern but suggested a slightly broader refactor.  **Qwen MoE** and **Gemma MoE** gave plausible answers but sometimes conflated multiple fields or proposed overly broad input sanitization.  The dense models were clearly ahead.

### Task 15241 — Mobile UI hit‑testing bug

Here **Gemma Dense** shone by focusing on the CSS stacking and pointer issues.  It correctly inferred that a badge overlay was intercepting pointer events and proposed `pointer‑events: none` inside a mobile media query.  **Qwen Dense** arrived at a similar fix but with a wider selector scope.  Both MoE models also identified overlap issues but framed the solution as broad layout rewrites, which were heavier than necessary.  This challenge highlighted Gemma’s conservative bias toward small fixes.

### Task 15273 — Offline comment duplication

This proved to be the most discriminating task.  **Qwen Dense** was the only model to correctly trace the missing `clientID` in the API request as the root cause and to propose re‑adding that parameter.  **Qwen MoE** also identified this issue but coupled it with an unnecessary secondary fix.  Both **Gemma** models focused on race conditions and identity loss during reconciliation; they proposed preserving `clientID` in the reconciled comment rather than restoring it in the outgoing request.  While their fixes prevented the duplicate display, they did not address the deeper cause of multiple API calls.  This task strongly separated the models on grounded causal reasoning.

## Final ranking and discussion

Aggregating the scores with the specified weights produced the following ranking:

1. **Qwen3.5‑27B (Dense)** — Top performer overall.  Its strengths include precise root‑cause analysis on the deepest task and balanced performance on the others.  Recommended as the default local coding assistant.
2. **Gemma 4 31B (Dense)** — Second place.  Conservative and reliable, excelling on the UI bug by favouring minimal CSS changes.  Serves as an excellent second opinion and safety net when Qwen’s suggestions seem speculative.
3. **Qwen3.5‑30B‑A3B (MoE)** — A strong showing for an MoE model.  Second on the hardest task and quite responsive.  Best used for brainstorming and generating candidate patches quickly, but its answers should be cross‑checked.
4. **Gemma 4 27B (MoE)** — Trailed in all tasks.  Delivered plausible but less grounded diagnoses and tended toward over‑general fixes.  Useful for scaffolding but not for final patch decisions.

### Dense vs MoE

The clearest pattern from the benchmark is the reliability gap between dense and MoE architectures.  Dense models engaged more consistently with the task instructions, anchored their reasoning to the actual patch or test, and proposed smaller fixes.  MoE models, while faster and often fluent, were more prone to drifting into plausible but incorrect explanations.  This suggests that for critical coding tasks where trust is paramount, dense models are worth the extra latency.

### Qwen vs Gemma

Within the dense models, there is no absolute winner.  Qwen tends to be a stronger debugger on deeper, multi‑file issues and currently boasts better open‑source benchmark results on SWE‑bench Verified and LiveCodeBench.  Gemma’s strength lies in its conservative fixes, particularly in UI contexts.  For users who can only choose one model, Qwen offers the better default coverage.  For those who can run two models locally, combining Qwen’s breadth with Gemma’s conservatism yields the best of both worlds.

## Recommendations for practitioners

* **Primary assistant:** Use **Qwen3.5‑27B (Dense)** as the main local coding model.  It combines reliable debugging with acceptable latency and strong public benchmark performance.
* **Secondary assistant:** Install **Gemma 4 31B (Dense)** for second‑opinion checks and tasks where minimal UI or CSS fixes are important.
* **Brainstorming:** Leverage **Qwen3.5‑30B‑A3B (MoE)** or **Gemma 4 27B (MoE)** for quick idea generation, code scaffolding and non‑critical drafting.  Always verify their outputs with a dense model before trusting them in production.

## Conclusion

This benchmark was intentionally small, focusing on quality over quantity.  It should not be taken as a universal leaderboard.  Instead, it provides grounded evidence for developers choosing local models for debugging and coding assistance.  Dense models consistently performed better than MoE models, and Qwen dense slightly edged out Gemma dense overall.  Users should treat these findings as one data point alongside wider benchmarks and their own experiments.
