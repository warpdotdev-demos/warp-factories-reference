# Triage

This stage is executed by the worker. Send all user questions, approval requests, answers, and delivery messages to the foreman for relay. Never declare inbox attention or complete the root task.

Triage establishes the state of the work. You research the issue, you reproduce bugs, and you create or update the work item that tracks the work. Triage does not spec, implement, or review the work; those are later stages, and this stage's outputs decide which of them runs next.

Start from the request, in its exact words, with the attached logs, screenshots, and links; from a reference to an existing work item, if one is known; and from what the conversation already established: the requester and the decisions already made.

Identify the user's goal first, from their words: an answer, a requested change, or unclear. Questions, diagnoses, opinions, and estimates ("can we do X?", "do we support Y?", "how does Z work?") end with an evidence-backed answer for the foreman, not implementation. Tracking an underlying problem may still warrant an issue; that is not permission to change it. An unclear goal becomes a scoping question. Only the user's reply or an explicit automation authorization turns an answer into a change.

## Outcome

- The existing-work discovery result: the search scope and terms, the matching task and PR links with their disposition, no match, or an unavailable lookup.
- The issue, created or updated, with the metadata and the content that the Issue content section specifies. The issue is the durable record.
- In hand when the stage ends: the work item reference, the complexity, the ambiguity that a spec must resolve (if you found ambiguity), the candidate approaches with your recommendation, and the settled pre-work choices. If you decided that an issue is not necessary, that decision stands in place of the reference.

## Procedure

1. Research. First complete existing-work discovery from the `pre-work-checks` skill for a new change. Read the request and the attached material. Read the applicable code. Make sure that you understand what is asked and where the problem or the change is. If you cannot clearly identify the problem, the missing information is a scoping ask that blocks this stage; see step 7.
2. Depending on the type of request, you may need to:
   - For a bug report: if the research in step 1 identifies the cause, a reproduction is not necessary. If the cause is not clear from the research, reproduce the bug if you can.
   - For a feature request: identify the requirements.
   - For an answer: return the evidence-backed answer to the foreman; do not turn it into an implementation plan. Track the underlying problem only if it deserves an issue, then end the stage with `goal: answer`.
   Once the cause or the requirements are known, assess the candidate approaches, with their trade-offs and your recommendation. Record these findings on the issue in steps 4-6.
3. Issue decision. Decide if the work needs an issue. If the work is too small to track, skip the tracker search, creation, and updates in steps 4-6 and note that decision; the conversation is the record. This skips tracking, not existing-work discovery or scoping.
4. Find or create the issue. Search for an existing issue that tracks the same work. If you find a clear match, adopt it. If the request is a follow-up to work that is already in progress (for example, an open factory PR or an issue that moves through the factory), do not create a parallel issue. Adopt that work. If there is no clear match, create a new issue. Keep the work item reference (for example, the URL) at hand; every later stage needs it.
5. For a new issue, set the metadata. Make your best attempt to set the correct status, project, dependencies, t-shirt size, priority, and other available metadata. The status must come from the team's workflow states and reflect the current stage of the work; the issue tracker skill has how to pick a state.
6. For an adopted issue, add the new information, if there is new information. Update the metadata if the new information changes it.
7. Scoping. Check every ask on the `pre-work-checks` scoping checklist (change wanted, approach, spec, clarifications). The open ones go to the user through this stage's `scoping` gate, which is also raised earlier when an ask blocks research or reproduction. When none are open, nothing opens. Preserve settled choices; do not ask them again.
8. End the stage with the work item reference, the complexity, the ambiguity that a spec must resolve (if any), and the settled choices in hand, and report the outputs below.

### Reproduction

- Attempt to reproduce a bug only when research cannot identify the cause. That is when the code, the logs, and the error output do not show what is wrong. When research identifies the cause, record the explanation and the evidence on the issue in place of a reproduction.
- When you reproduce, operate the affected code path. See the incorrect behavior yourself.
- Prefer a test as the reproduction. Write a small test that fails because of the bug. This test is also the regression test for the fix. If a test is not practical, call the affected function or service directly. Record the incorrect output.
- For a bug in a user interface, reproduce the bug in the interface with the computer-use tool and capture visual proof. Read the `ui-verification` skill for the capture standard and procedure.
- If you cannot identify the cause and you cannot reproduce the bug, do not continue silently. Possible causes are missing information, or an environment that you do not have (for example, production-only data). Record what you tried on the issue. Ask the foreman to obtain more information or help.
- Post the reproduction proof on the issue: the output of the failed test, the incorrect response, or the visual proof.

### Candidate approaches

Once you know the cause or the requirements, look for the different ways the work could be done before you settle on one. Ask, at least: where could the change land (the call site or the shared code, the client or the server, a migration or a read-time fix); what could the resulting behavior be; what could be left out; and what each option costs or risks. Write down every approach that a reasonable engineer might pick, one or two sentences each with its trade-off, and then say which one you recommend and why.

A single approach is a fine result when you looked for alternatives and they are clearly worse; say that you looked. It is not a fine result when it is only the first approach you saw. Do not narrow to one path to make the report tidy: a report that hides a real choice sends the factory into its most expensive stage on a guess, and that costs far more than the lines it takes to list the options. The user chooses through the foreman, or explicitly delegates the choice to you.

### Issue content

Write a clear, concise title that describes the work.

Write the description for the next factory step, not as a research log. Write in the spirit of ASD-STE100 (Simplified Technical English): short sentences in the active voice, one idea per sentence, one meaning per word used consistently, and no vague qualifiers ("as needed", "appropriately", "etc."). Prefer bullets over long prose.

Hard limits:
- Description: at most 5 short sentences.
- Acceptance criteria: at most 5 bullets, each an executable check.
- Do not narrate the research sequence. Put code locations, test output, and links in References, not in Description.

Adopt the style of existing issues if there is a consistent one. Otherwise, structure the issue description with the following template (omitting sections when they do not apply):

```markdown
## Description
What is wrong or what is asked, and where. At most five short sentences.

## Reproduction
The reproduction steps and the proof: the failed test output, the incorrect
response, or the visual proof. When research identified the cause without a
reproduction, the explanation and the evidence instead.

## Acceptance criteria
The checks that show that the work is complete. Each check is executable.

## Candidate approaches
The approaches you considered, each with its trade-off, and the one you
recommend and why. This is input to the next steps, not a decision.

## References
Specific code locations, test results, error output, and applicable links.
```

The 'References' section is important: without it, the subsequent factory steps have to collect this context again, which is slow and costly.

### Complexity, ambiguity, and choices

- Report the complexity as the t-shirt size that you set on the issue:
  - XS or S: the change is local and obvious, with low risk.
  - M: the change touches multiple files, with bounded uncertainty.
  - L or XL: the change is cross-cutting, has risk, or is a full feature.
- Tell three kinds of uncertainty apart, and report each in its own place:
  - Facts that research can settle - where the bug is, what the code does today, which callers are affected - are not ambiguity. Settle them and record the evidence.
  - Behavior choices - more than one behavior is reasonable, or the request does not specify important requirements - are ambiguity. Report them as the ambiguity that a spec must resolve.
  - Approach choices - more than one reasonable way to do the work, with trade-offs a user would care about - go in the candidate approaches. They are the foreman's cue to relay the choice to the user before implementation, so leaving one out is not a tidier report, it is a missing warning.
- Do not invent ambiguity where the request is clear, and do not hide a real choice by picking for the user. Both are errors; the second is the more expensive one.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `goal`: `answer` (the evidence-backed answer is the deliverable) or `change` (a change is wanted). A user who declines the change says so in the `scoping` answer; that is not an output.
- `next`: for `goal: change`, the next stage: `spec` (the user agreed to a spec) or `implement` (no ambiguity or decision needs a spec, and every applicable ask is resolved).
- `findings`: existing-work discovery, the issue decision and reference, the evidence, the complexity, the ambiguity, and the candidate approaches.
- `decisions`: every settled pre-work choice, from the request or the `scoping` answers, in the user's words.

## Skills

Read a skill before your first operation on its surface, and use only the skills that the work needs. Resolve each skill's path from the available skills catalog.
- `pre-work-checks`: existing-work discovery and the scoping checklist.
- `ui-verification`: capturing visual proof when you reproduce a bug in a user interface.
