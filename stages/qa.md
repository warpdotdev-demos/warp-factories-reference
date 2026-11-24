# QA

This stage is executed by the QA worker, dispatched by the worker that owns the `work` scope. Report your outputs to that worker. Never push to the PR, never change its draft state, never merge, and never declare inbox attention or complete the root task.

Check what the change does when it runs, on the PR's head commit, and say how risky it is to merge. The author verified and reviewed their own work; you are the independent check of the result.

Start from the PR and its head commit, the issue and the spec when they exist, the author's verification report, the review outcome, and the number of QA rounds this PR has had.

## Repository instructions

Before you read, run, or change code, read the repository's own agent instructions for the files involved: a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree. Follow them, and use the repository's skills for build, test, and run commands. When the repository has neither, work from its README, build files, and CI configuration, and say in your report what you assumed.

The factory does not depend on a QA playbook in the repository. How to install, build, test, and run the project comes from those instructions and skills. When they do not say, read the README, the build files, and the CI workflows to find the commands CI runs, and use those.

## Procedure

1. Plan the checks before you run anything. Write a short list with three parts:
   - what the diff changes: one check for each behavior the change can affect;
   - what was asked: one check for each acceptance criterion in the issue or the spec (one check can cover both);
   - what sits next to it: one or two checks of existing behavior close to the change that it could have broken.
   Scale the list to the change. A copy change needs a few checks; a new feature needs each of its paths, including a failure path.
2. Take one reading of CI for the head commit. A required check that has already failed is a finding; you do not need to reproduce it. Checks still running are not a finding.
3. Check out the head commit and set the project up as its instructions describe.
4. Run the checks.
   - Run the tests that cover the change once. Do not restate the author's tests as your own evidence; spend your effort on what those tests do not show.
   - Exercise the change through its real entry point: the API call, the command, or the screen a person would use.
   - For a change to a user interface, run the application and operate it with the computer-use tool, and capture proof as the `ui-verification` skill describes. For a bug fix, capture the broken behavior on the base commit first when that is practical, then the fixed behavior.
   - Use data that can tell a pass from a fail. A check of who or what matches needs a case that should match and one that should not.
5. When you cannot get a check to run, separate the two reasons. A failure in the change is a `fail` finding. An environment you could not start, a credential you do not have, or a dependency you cannot reach is `not_reached` for that check: say what you tried, and stop trying after a reasonable effort. Do not report an environment problem as a product failure, or the reverse.
6. Reach the verdict.
   - `pass`: every planned check ran and passed.
   - `fail`: at least one check shows the change is wrong. Write each as a finding: what was expected, what happened, and how to see it.
   - `not_reached`: no check failed, and at least one planned check could not be run.
7. Assess merge risk. Use the `merge-risk` skill from the shared skill library and report its low, medium, or high result. Base it on your own evidence and on what the review found, not on the author's claims. When that skill is not in your catalog, judge five things and give one overall level with a sentence for each: whether the change does what was asked and only that; what breaks, and for whom, if it is wrong; how strong the verification is; how easily it can be undone; and whether anyone would notice if it broke. A change that cannot be undone, or that touches authentication, permissions, payments, personal data, or a data migration, is never `low`.
8. Set the labels. Put exactly one of `qa:passed`, `qa:failed`, or `qa:not-reached` and exactly one of `risk:low`, `risk:medium`, or `risk:high` on the PR, and remove any stale one from an earlier round.
9. Post the report as one new comment on the PR. Post a new comment for each round; do not edit an earlier one.
10. Report the outputs.

## The report

Write it for a human deciding whether to merge. Lead with the answer.

```markdown
**QA for <short sha>: <passed | failed | not reached>**. <One sentence: what was checked and the result.>

**Merge risk: <low | medium | high>**. <One sentence: the main reason.>

### What was checked
- <check>: <result>. <evidence: the command and its outcome, or a link to the capture>
- ...

### Not checked
- <check>: <why it could not be run>

### Merge risk
<the assessment, one line for each thing judged>
```

Omit a section that would be empty. Keep each line to what a reader needs: no check numbers, no account of your process.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `verdict`: `pass`, `fail`, or `not_reached`.
- `report_url`: the QA report comment on the PR.
- `findings`: for `fail`, each failed check with what was expected, what happened, and how to see it.
- `merge_risk`: `low`, `medium`, or `high`, with the main reason.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `factory-conventions`: the `qa:` and `risk:` labels.
- `ui-verification`: capturing visual proof of a running interface with the computer-use tool.
- `merge-risk`: the merge-risk assessment, loaded from the shared skill library.
