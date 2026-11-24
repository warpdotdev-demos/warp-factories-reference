---
name: Stage Transition Compliance
description: Whether the agent declared the blueprint stage moves it drove, with correct paths, timing, and ownership.
workers:
    - Engineering Task-Worker
    - Engineering Foreman
    - Engineering Security
labels:
    - value: consistent
      description: Every observable stage move was successfully declared before the destination's work, using the correct scope and paths and honoring the applicable blueprint edges and gates.
      score: 1
    - value: noncompliant
      description: An observable move was missing, late, incorrect, or outside the agent's ownership, or a transition bypassed an open gate.
      score: 0.2
    - value: not_applicable
      description: 'The visible run required no stage move: it stayed in the same stage, waited on a gate, or performed an explicitly exempt bounded operation.'
      score: 1
    - value: insufficient_evidence
      description: Missing blueprint, stage context, conversation coverage, or tool results prevent a reliable judgment, and no definite violation is visible.
      score: 0.5
passingScore: 0.5
samplingRate: 10
model: gpt-5-6-luna-xhigh
selfImprovement: true
---
**Rubric**

Evaluate whether the agent recorded the blueprint stage moves it actually drove using the `transition_stage` tool. Check coverage and correctness, not merely whether the tool appears somewhere. Judge against the blueprint and instructions in effect in the visible transcript; do not assume every task must visit every stage.

1. **Reconstruct the moves from work, not from declarations alone.** Identify entry into a scope's first stage, movement between stages, dispatch and return boundaries, and completion of a scope. Compare the actual work and handoffs with the calls. Reading a destination's procedure in preparation is not by itself entry into that stage. An explicit exemption for a bounded operation takes precedence over the normal lifecycle: a relayed automation's bounded operational procedure does not enter the lifecycle graph.
2. **Require an actual call and successful result.** A prose announcement, a tracker status update, an inbox attention declaration, `complete_task`, or `finish_task` does not substitute for `transition_stage`. Recognize a call routed through a generic tool wrapper by its inner tool name and arguments. Quoted examples, plans to call it, and unrelated tool calls are not declarations. Judge whether the correct call succeeded before destination work; a timely successful retry satisfies this check.
3. **Check paths and scope ownership.** Use full paths from the root, such as `work/triage` to `work/implement`. Scope entry uses `<scope>/start`; scope completion uses `<scope>/end`; the root uses bare `start` and `end`. The agent that drives a scope declares that scope's moves: the root scope's driver declares entry into a root stage, moves between root stages, and root `end`; the driver of a nested scope declares the moves inside it, including its own `<scope>/start` and `<scope>/end`. These are separate scope boundaries, not duplicate calls. An agent that only executes a dispatched leaf stage declares nothing. Reaching a nested scope's `end` does not complete the root task or authorize a call to root `end`.
4. **Check sequence, conditions, and timing.** A call must precede the destination's work, leave the current stage in that scope, name the actual destination, and follow the applicable edge or explicit authorized entry. A successful tool response proves capture, not that the agent chose the right edge or satisfied its conditions. Flag a late retrospective call, a nonexistent or wrong path, a skipped required stage, or a premature move past an open gate or an unapproved spec. Do not impose a fixed sequence: direct entries, skips, loops back to an earlier stage, and escalations are allowed when the blueprint's edges permit them and their stated conditions hold.
5. **Respect gates and continuations.** Gates are waits inside a stage, not stages: they need no `transition_stage` call, and a stage cannot be left while one is open. A paused stage remains active; resuming it after the answer does not require a fresh start call. A nested scope may reach its `end` and report a delivered PR while its parent stage stays active with a gate open; do not demand a merge or a root-end call while the PR is still awaiting a human. When that gate returns more work, the nested scope is re-entered from its `<scope>/start`. Revision self-edges and other same-stage calls are no-ops: their presence or absence neither substitutes for a required move nor proves a violation on its own. Judge resumed work using the visible prior state, not an assumption that every new run starts at `start`.
6. **Respect evidence boundaries.** Keep each conversation's and scope's ordering separate; interleaved parent and child transcripts are not one flat stage sequence. Do not blame one agent for calls outside its responsibility or infer omissions in an unseen child. If relevant evidence is missing, use `insufficient_evidence`, unless a definite violation is already visible.

Choose `noncompliant` for any definite violation even if other calls were correct or later repaired. Otherwise choose `insufficient_evidence` when the record cannot establish compliance, `consistent` for successfully declared moves, or `not_applicable` only when the visible work affirmatively required no move. A transcript with no calls is not automatically inapplicable: visible stage work can establish a miss.

Out of scope: code quality, general task fulfillment, and root completion tool compliance, except as evidence of an actual or premature stage move. Do not invent violations or demand calls beyond the visible work.

**Reason**

One to three sentences identifying the responsible agent and scope, the expected `from_stage` / `to_stage`, and the observed call, omission, timing, or gate. For `not_applicable`, explain why no move was due; for `insufficient_evidence`, name the missing evidence.
