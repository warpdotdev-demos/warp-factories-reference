# Spec

This stage is executed by the worker. Send all user questions, approval requests, answers, and delivery messages to the foreman for relay. Never declare inbox attention or complete the root task.

The spec (aka plan) stage decreases ambiguity before work starts, captures constraints and intentional trade-offs, aligns on product, design, and engineering decisions, and creates a durable reference for implementation. The stage does not implement or review the work.

Start from the work item: the issue seeds your context with the request, the findings from triage, the code locations, and the reproduction. Start also from the ambiguity that triage recorded, if it found ambiguity, and from what the conversation already established: the requester and the decisions already made.

## Preconditions

Every applicable ask on the `pre-work-checks` scoping checklist is resolved, and the existing-work discovery result is recorded. On a direct entry into this stage, run existing-work discovery first; asks that are still open go to the user through this stage's `scoping` gate before any spec work. Carry resolved choices in; do not ask them again.

## Outcome

- The specs: a product spec, a tech spec, or both, committed as a file in a draft PR. The implementation stage reuses this PR and its branch. The committed file is the durable reference during implementation.
- In hand when the stage ends: the spec PR reference, the decisions that the user made during the interview, and the assumptions that you recorded. The stage ends through its `approval` gate: the user must approve the current revision before implementation starts.

## Procedure

1. Seed context. Read the issue and the references in it. Read the applicable code to fill the gaps. Do not collect again what the issue already contains.
2. Interview. Interview the user to resolve the ambiguity before you write. See the Interview section below. An interview can take more than one round of questions, but each round is a stop for the user, so batch the questions into as few rounds as possible.
3. Choose the format. Write a product spec when the open questions are about behavior. Write a tech spec when the open questions are about implementation. Write both when both are open.
4. Write the specs. See the Spec content section below.
5. Commit the spec. Commit the spec as a file on a new branch and open a draft PR. Apply this factory's label to it - the code forge skill resolves the label and has the commands. Ensure there is an attribution comment on the PR, and post one if there is not - the code forge skill has the check and the mechanics. Record the PR reference on the issue, when the work is tracked.
6. Approval. The committed spec goes to the user through this stage's `approval` gate. The foreman's acknowledgement is not approval. Approval may come as a written confirmation or as approval of the draft PR.
7. Revise, when the user requests changes; the stage is entered again with the feedback. The committed file is the source of truth, and a human can edit it directly. Read the committed file and the comments on it again before you revise. Batch each round of changes into as few file edits as the round needs - one consolidated edit per revised section, not one per sentence or hunk. Commit the revision to the same branch and PR. Do not open a second spec PR. The revised spec needs its own approval.

### Interview

- You are the interviewer, and the user is the interviewee. Interview the user before you write. The goal is that the user fully understands and explicitly agrees with what will be built. Do not silently write a spec that could be correct.
- Batch the questions. Each round is a stop for the user, and each stop is slow. Collect all of the open questions first. Then send them as one batch. Send a second round only for questions that the answers to the first round created.
- Make each open fork an interview question: each design choice, each assumption, and each unclear scope boundary.
- Interview critically, do not only clarify. Push back on vague expected behavior. Ask if the approach corrects the root cause or only the symptom. Raise trade-offs and alternatives that the user did not consider.
- Do not ask a question that the issue already answers.
- For a bug, ask: What is the correct behavior, exactly? What are the constraints on the fix? What is out of scope?
- For a feature, ask: What is the primary user workflow? Which edge cases and failure modes are most important? Which alternatives were considered and rejected? What is not in scope? Which existing patterns must the change follow?
- If the user tells you to proceed with questions unanswered, choose the most careful interpretation for each one. Record each choice as an assumption in the spec.
- End the interview when you are aligned, and say so. Then write the spec.

### Spec content

This factory loads spec procedures from a shared skill library. When `write-product-spec` and `write-tech-spec` are in your skills catalog, use them for the content and shape of the spec: `write-product-spec` when the open questions are about behavior, and `write-tech-spec` when they are about implementation. Two things differ here from what those skills say:
- Questions for the user go to the foreman for relay. Do not ask the user directly, and do not use an interactive question tool.
- Name the spec directory for the issue that tracks the work, or for the feature when the work is not tracked. Do not create a ticket to get a name.

The rest of this section applies to every spec, and is the whole standard when those skills are not in your catalog.

- Make the spec self-contained. The implementation stage must not have to make a significant design decision. Resolve each question from the code, the issue, or the interview answers. Record the remainder as assumptions and make it clear what was an assumption.
- Write in clear, unambiguous language, in the spirit of the ASD-STE100 (Simplified Technical English) guidelines: short sentences in the active voice, one instruction per sentence, one meaning per word used consistently, and no vague qualifiers ("as needed", "appropriately", "etc."). A spec is read under time pressure; it must not be open to interpretation.
- Use bulleted lists where possible in place of long prose. Lists are easier to scan, to reference, and to check off during implementation and review.
- Record the constraints and the intentional trade-offs. Say what was deliberately not chosen, and why.
- For each real decision point, record the alternatives: the options, their advantages and disadvantages, and why the winner won.
- The validation criteria are the most important part. Make them objective, executable, and complete. Each criterion says how it is checked: a test name or a command. For a change in a user interface, require visual proof with the computer-use tool: video by default, screenshots only for a genuinely static render.
- Include code references and snippets when they remove ambiguity for the implementation: the exact files and functions to change (`path/file.rs:line`), the existing patterns to follow, and short snippets that show the intended shape of an interface or a change (a function signature, a schema, a config entry). Do not write the full implementation; a snippet illustrates a decision, it does not replace the work.
- Scale the spec to the work. For a small change, a short spec is sufficient, and the validation criteria are most of it. For a large change, add a product section (numbered, testable behaviors, with edge cases) and a tech section (how the area works today, the proposed changes, and the data flow).

Structure the spec with this general template. Omit a section when it does not apply, per the scaling guidance above.

```markdown
# <Title>

## Summary
What is being built or fixed, and why. One short paragraph.

## Product behavior (for product spec)
Numbered, testable user behaviors.

## Technical design (for tech spec)
How the area works today, the proposed changes, and the data flow. The files
and functions to change, with code references and snippets where they remove
ambiguity.

## Decisions
Each real decision point: the options, their advantages and disadvantages,
and why the winner won.

## Assumptions
The choices made without a user answer, each marked clearly as an assumption.

## Out of scope
What was deliberately not chosen or deferred, and why.

## Validation criteria
Objective, executable checks that show the work is complete. Each criterion
says how it is checked: a test name, a command, or required visual proof.
```

## Outputs

Report these when the stage ends; the blueprint's edges read them together with the `approval` answer, which is the user's and not an output.
- `spec_pr`: the draft PR that holds the committed spec, on the branch that implementation reuses, linked to the issue when one exists.
- `decisions`: the choices that the user made during the interview and the assumptions that you recorded.

## Skills

Read a skill before your first operation on its surface, and use only the skills that the work needs. Resolve each skill's path from the available skills catalog.
- `pre-work-checks`: existing-work discovery and the scoping checklist, for a direct entry into this stage.
- `write-product-spec`, `write-tech-spec`: the content and shape of the spec files, loaded from the shared skill library.
