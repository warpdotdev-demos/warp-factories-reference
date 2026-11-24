---
name: Efficiency
description: Whether the agent worked directly toward its result, or wasted effort on redundant steps, avoidable rework, or unnecessary back-and-forth.
workers:
    - Engineering Task-Worker
    - Engineering Foreman
labels:
    - value: highly_efficient
      description: 'The agent took a direct path: nothing re-read or re-run, independent steps batched, no work redone.'
      score: 1
    - value: mostly_efficient
      description: 'The agent slipped once or twice: a duplicated read, an early retry, or a small correction — with no knock-on cost.'
      score: 0.8
    - value: mostly_inefficient
      description: The agent wasted effort repeatedly, or caused a round of rework an earlier check would have prevented.
      score: 0.4
    - value: highly_inefficient
      description: 'The agent''s waste dominated the run: the same defect reworked across cycles, repeated user correction, or extended flailing / looping.'
      score: 0.2
passingScore: 0.5
samplingRate: 10
model: gpt-5-6-luna-xhigh
selfImprovement: true
---
**Rubric**

Evaluate the full cost of reaching the result using recorded cost, duration, and tool-call metrics from Platform facts when present, plus the steps the agent took, rework it caused elsewhere in the loop, and human attention it consumed. Score against what a competent engineer with the same tools would have needed, not against what was achievable with only what the agent happened to have. A mistake that looks unavoidable in context still counts if better tooling, a skill, or a check would have prevented it — name that cause in the reason. The bullets below are common sources of waste, not an exhaustive checklist — judge any other way the run cost more than it should have.

Assess:

- **Rework from mistakes.** Work redone because the agent got it wrong the first time: a review or CI failure that the required targeted local checks would have caught, edits to the wrong file, a misread requirement later reverted. Do not penalize an uncommon CI failure merely because a broad local suite or presubmit, intentionally left to CI, would have caught it.
- **Iteration cycles.** In a coordinating run, the round trips needed to reach an acceptable result — repeated implement-and-review cycles, work bounced back for the same defect. Attribute the cost to this run where its own choices caused it: an underspecified brief, unchecked acceptance of work, not passing forward what a prior cycle established.
- **Cost to the human.** Repeated correction or steering from the user is the most expensive waste. A question asked up front is cheap; the same question asked after building the wrong thing is not.
- **Proportionality.** Effort spent on a stage the ask did not call for: an implementation for a question, a spec for a one-line fix, a reproduction when the cause was already in the logs. Implementation that nobody asked for, or that took a shape the user would not have chosen, is the most expensive form; charge it to the run that dispatched it, and say whether one question up front would have avoided it.
- **Information gathering.** Re-reading, re-running, or re-searching for something already found; reading a large file end to end when a targeted search would answer it.
- **Routine-step overhead.** A roundabout way of doing something that's a standard, repeated part of this agent's job — more steps, more calls, or a broader operation than the step needs — when a more direct path was available. Weight this beyond its one-run cost: the same avoidable overhead recurs on every future run of the same type until the pattern is fixed.
- **Batching.** Independent reads, searches, or workstreams run serially across turns instead of together.
- **Flailing.** Retrying a failing approach unchanged, or guessing when reading the code or docs would have settled it. An abandoned path only counts against the agent when the information to avoid it was already available.
- **Verification timing.** Targeted checks provide feedback while the agent works. Once the candidate settles, relevant tests run to green, then lint, build, and typecheck checks run to green, then applicable formatters run once. Repeating broad tests, lint, formatting, or presubmit without an intervening change that invalidates the result is substantial waste; running a full presubmit when neither repository guidance nor the task explicitly required it is also waste.

**Reason**

One to three sentences naming the dominant source of waste with a rough count (three review cycles, four redundant reads, two repeated user corrections), and the likely fixable cause — a missing skill, an ambiguous playbook step, a late check.
