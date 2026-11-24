# Investigate

This stage is executed by the responder, inline in the `respond` scope. Send questions and findings to the foreman for relay. Never declare inbox attention or complete the root task.

Find what caused the problem, with evidence a reviewer can check, and decide what to do about it. Do not change code in this stage; `fix` does that.

Start from what `confirm` established: the problem, when it started, the last state known to be good, and the issue.

## Procedure

1. List what changed between the last good state and the first bad one. For a build, that is the commits on the default branch between the two (`git log --oneline <good>..<bad>`), and the PR behind each commit.
2. Read the failure itself: the failing assertion, the error message, the stack trace, the log lines. Note the files and functions it names.
3. Tie the failure to a change. The cause is the one change whose diff contains the code the failure lands in, or whose removal makes the failing check pass. When the range is long and the failure reproduces locally, bisect it (`git bisect`) with the failing check as the test.
4. Consider causes outside the repository's history before you blame a commit that only happens to be recent: a dependency that resolved to a new version without a lockfile change, an external service, an expired credential, a change to CI configuration or runners, or a test that depends on the date. When one of these fits the evidence better than any commit, say so.
5. Decide.
   - `revert`: one merged change caused the problem and reverting it applies cleanly. This is the default: it returns the code to a state that was working, and it is easy to undo.
   - `fix`: later changes build on the offending one so that a revert does not apply cleanly, or the correction is smaller and more plainly right than the revert, such as a one-line mistake.
   - `human`: the evidence points at a suspect but does not prove it; the remedy would be hard to undo (a data migration, a change to a published contract, data loss); or a revert does not apply cleanly and a fix needs a product decision. This opens the `human` gate.
   - `no_cause`: nothing in the range or outside it fits the evidence. Say what you checked.
6. Record the cause on the issue, in a section a person can follow without your transcript: the failure, the change it ties to, the evidence for the tie (the failing output and the lines of the diff it lands in), and the decision.
7. When a PR is the cause, leave one short comment on that PR with the issue link, the evidence, and the decision. When a person wrote it, address them by their code forge login and say what happens next: a revert is being opened, which they can merge, or they can fix forward on their own branch instead.
8. Write the brief for `fix`: the change to revert or the root cause to correct, its author, and the check that will show the problem is gone.

## Revisions

New evidence after you recorded a cause updates that section in place and gets one new comment that says what changed. Never record a second, conflicting cause without saying so.

A human's answer to the `human` gate comes back to this conversation through the foreman. Do what it says: `revert` or `fix` continue to the `fix` stage, and `leave as is` ends the lane with the evidence recorded.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `cause`: the offending change with the evidence that ties it to the problem, a cause outside the repository's history, or none.
- `decision`: `revert`, `fix`, `human`, or `no_cause`.
- `brief`: for `revert` or `fix`, the change to undo or the root cause to correct, its author, and the check that proves the problem is gone.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `factory-conventions`: where findings go on the issue.
