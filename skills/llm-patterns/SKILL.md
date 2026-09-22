---
name: llm-patterns
description: Helps people who are new to or unfamiliar with LLMs understand puzzling behavior by diagnosing recurring patterns and explaining the model's priors, inertia, and habits. Use when instructions appear to be ignored, the model becomes fixated on an unintended direction, or its behavior is otherwise hard to understand. This skill explains behavior but does not directly correct or change it.
---

# $llm-patterns — Diagnosing Recurring LLM Failure Patterns

## Purpose

Help users who are new to or unfamiliar with LLMs understand puzzling model behavior.
When a user asks why a recurring behavior or deviation occurred, classify it as one of patterns P-001 through P-012 and explain the model's priors, inertia, or habits that contributed to it.
Limit the response to diagnosis and causal explanation. Do not present the skill as directly correcting or changing the model's behavior.

## Inputs

1. Identify the behavior or recurring deviation reported by the user.
2. Determine whether any relevant rules, skills, tools, or contextual instructions were active.

## Diagnostic Procedure

1. Determine whether a rule requiring or prohibiting the behavior was actually present in the model's context.
2. If the rule was absent or was not delivered to the model, classify the issue as a `rule visibility problem`.
3. If the rule was visible but the behavior still recurred, or if the deviation was probabilistic and unrelated to a specific rule, compare it against the pattern catalog below.
4. Select the single most direct pattern as the primary classification. List overlapping patterns only as secondary classifications.
5. Structure the response in this order: `classification → observed evidence → causal explanation`.

## Pattern Catalog

### P-001 Structured Data Attention Bias

When many nested fields are provided at once, action-oriented fields may be used before corrective or negative-evidence fields. As a result, fields such as `mistakes`, `rejections`, or `drift` can be present in the context but omitted from the reasoning process.
Apply this pattern when structured data was available but the same group of fields was repeatedly ignored.

### P-002 Error-Attribution Self-Blame Bias

After an error is pointed out, responsibility-accepting phrases such as “I made a mistake” or “I should have been more careful” appear before the actual cause is classified.
Apply this pattern when attribution stops at self-blame without determining whether the cause was a rule violation or a structural failure pattern.

### P-003 Context Dilution

As rules and explanations accumulate, the relative signal strength of each individual rule decreases. A rule that worked in a short context may therefore be repeatedly missed in a longer one.
Apply this pattern when adding more rules makes compliance worse rather than better.

### P-004 Helpful Over-delivery

A narrow request—such as checking, looking up, or reading something—is automatically expanded into analysis, recommendations, execution, or a list of options.
Apply this pattern when the drive to produce a “more helpful” response takes control away from the user's literal scope.

### P-005 Reliability-Weighted Knowledge Conflict

When parametric knowledge conflicts with retrieved or contextual evidence, the winning source depends on factors such as confidence in the prior, the popularity of the information, and the consistency and reliability of the external evidence.
Do not reduce this pattern to the old `Parametric Memory Fallback` framing or assume that the model always returns to its prior. The reverse failure—incorrect retrieved evidence overriding a correct prior—is also included.

### P-006 Conditional Branch Attention Dilution

As a skill or procedure accumulates more conditional branches and operating modes, some branch conditions are missed and execution drifts into a different path.
Apply this pattern when the same branch violation continues even after more rules are added.

### P-007 Length-Induced CoT Degradation

In a long context, the model can become increasingly conditioned on its immediately preceding reasoning. Exploration of new options weakens, and premature conclusions such as “there is no further improvement” or “this is the limit” become more likely.
A strong diagnostic signal is that the same question produces a new alternative immediately when asked in a fresh session.

### P-008 Test-Suite Reward Hacking

Instead of satisfying the specification, the model optimizes the visible proxy objective of passing the tests through test modification, hardcoding, or special-case handling.
Apply this pattern when the tests pass but the actual requirement is still violated.

### P-009 Package/API Hallucination

Without inspecting the real environment, the model reconstructs names from learned patterns and confidently produces packages, APIs, or symbols that do not exist or have been deprecated.
Unlike P-005, the core issue is not a conflict between knowledge sources but parametric generation without verification evidence.

### P-010 Action Repetition Loop

The model repeats the same tool call with the same arguments, or replays the same failed action, even though the state has not changed.
Apply this pattern when the previous thought-action pair conditions the next action and consumes the available step budget without progress.

### P-011 Cascading Failed Edits

After an initial edit damages the file state, the model continues stacking patches on top of the damaged state instead of restoring a known-good baseline.
Apply this pattern when syntax or structural errors multiply with each additional edit.

### P-012 In-Session Sycophantic Anchoring

The model over-accommodates user pushback or its own immediately preceding conclusion, causing it either to reverse a correct answer or to rationalize an existing conclusion more strongly.
Unlike P-007, the main trigger is user disagreement or the model's prior in-session conclusion, not context length itself.

## Structural Interpretation Guidelines

1. Treat repeated violations first as a possible structural problem, not as a simple failure to “pay more attention.”
2. Evaluate how paths, schemas, tools, and output structures compete with the model's default probability distribution before assuming that more textual rules will solve the problem.
3. Check whether the desired behavior has been designed as the path of least resistance rather than merely protected by prohibitions.
4. Before recommending additional rules, consider whether doing so could recreate P-003 or P-006.

## Output Format

1. `Primary classification: P-00X — Name`
2. `Observed evidence:` Signals from the user's report that directly match the pattern
3. `Causal explanation:` One to three sentences explaining why the pattern produced the observed result
4. Add a one-line `Secondary classification:` only when another pattern materially overlaps.

## Completion Criteria

- Exactly one primary classification is stated clearly.
- Causes not provided or supported by the user's evidence are not presented as facts.
- A diagnosis request has not been expanded into corrective action or implementation.
