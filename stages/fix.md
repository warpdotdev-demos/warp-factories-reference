# Fix

This stage is executed by the responder, inline in the `respond` scope. Send questions and findings to the foreman for relay. Never declare inbox attention or complete the root task.

Carry out exactly the decision from `investigate`, a revert or a bounded fix, as a draft PR against the default branch. Never push to the default branch, never deploy, and never merge.

Start from the brief: the change to revert or the root cause to correct, its author, and the check that proves the problem is gone.

## Repository instructions

Before you read, run, or change code, read the repository's own agent instructions for the files involved: a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree. Follow them, and use the repository's skills for build, test, and run commands. When the repository has neither, work from its README, build files, and CI configuration, and say in your report what you assumed.

## Procedure

1. Branch from the latest default branch.
2. Make the change.
   - Revert: `git revert <sha>` for a squash or single commit, or `git revert -m 1 <merge commit>` for a merge commit. Do not resolve a conflict by hand: a conflict means the revert is not clean. Stop and report that to the foreman, with the conflicting files, so a human can choose between a fix and leaving it.
   - Fix: the smallest change at the root cause named in the brief. Follow the `code-quality` skill and the repository's instructions. No refactors and no unrelated cleanup. Add a regression test that fails before the change and passes after it, unless the change is exempt from testing (configuration only, a version bump, a constant, or copy).
3. Verify. The check named in the brief must pass: run the failing job's commands, or the failing test, as the repository's instructions describe. Then run the scoped checks for the files you changed (tests first, then lint, type, and build checks, then the formatter once). Record each command and what it proved. When you cannot run a check, say so in the PR description and in your report; never claim that an unverified change passed.
4. Open a draft PR.
   - Title: `<ISSUE-KEY>: Revert "<original title>"` or `<ISSUE-KEY>: <what the fix does>`.
   - Labels: this factory's label and `lane:respond`. The code forge skill resolves the factory label and has the commands.
   - Description, using the repository's PR template when one exists, otherwise these sections:
     - Why: the problem, the cause, and the evidence that ties them together, with links to the issue and the offending PR. When this reverts a person's PR, address them by login and say that they can merge this or fix forward on their own branch and close it.
     - What: the change. For a revert, say that re-applying the original is a new change, not a revert of this one.
     - Verification: what you ran and what it proved.
   - Ensure there is an attribution comment on the PR; the code forge skill has the check and the mechanics.
5. Record the PR on the issue. Leave the PR a draft: the review stage readies it when the review accepts it.
6. When the brief was a revert of a change that was itself wanted, create one follow-up issue for doing that work again without the problem: what the original change was for, why it was reverted (the evidence in two or three sentences, with the links), and what must keep working. Do not start it. Report its key; a person starts it.

## Revisions

Review findings and CI failures come back here as revisions of the same PR. For each review finding, make the change or say in the thread why you did not, then reply in the thread and resolve it with the code forge skill's `resolve-threads` script. Verify again. Keep the change inside the problem: a finding that asks for more becomes a follow-up issue, not more of this PR. Never open a second PR.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `pr`: the draft PR.
- `kind`: `revert` or `fix`.
- `verification`: what was run and what it proved, and anything that could not be run.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `code-quality`: the standard for the fix. Read it before you write code.
- `factory-conventions`: the labels this PR carries and where it is recorded.
- `resolve-merge-conflicts`: when the branch falls behind the default branch during a revision, loaded from the shared skill library.
