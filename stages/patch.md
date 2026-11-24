# Patch

This stage is executed by the security worker, inline in the `security` scope. Send questions and reports to the foreman for relay. Never declare inbox attention or complete the root task.

Open the smallest verified upgrade for an advisory that `assess` found `affected`, as a draft PR. Never merge.

Start from the brief: the target versions, the compatibility changes to expect, and the residual risk.

## Repository instructions

Before you read, run, or change code, read the repository's own agent instructions for the files involved: a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree. Follow them, and use the repository's skills for build, test, and run commands. When the repository has neither, work from its README, build files, and CI configuration, and say in your report what you assumed.

## Procedure

1. Branch from the latest default branch.
2. Change the smallest version that removes the vulnerable one. Prefer upgrading the direct dependency that brings the package in. Use an override or a pin only when no direct upgrade reaches a patched version, and never one whose only effect is to quiet a scanner while the vulnerable version stays resolved. Follow any rule the repository has about dependency changes, such as a minimum release age or an allowlist; do not loosen such a rule for the whole repository to get one package through.
3. Regenerate the lockfile with the repository's package manager, and confirm that nothing unrelated changed.
4. When the upgrade needs code changes to keep working, make only those, to the `code-quality` standard. Stop and report to the foreman when the upgrade turns into a rewrite or changes behavior that users see: that is a decision for a human.
5. Verify.
   - Install from the lockfile exactly as CI does, and read the resolved versions: every copy of the package is at a patched version.
   - Run the audit tool again: the advisory is gone. Report other findings that were already there separately; do not fix them in this PR.
   - Run the build, lint, and type checks, and the tests for the code that uses the package.
   - Record each command and what it proved. When you cannot run a check, say so in the PR description and in your report; never claim that an unverified change passed.
6. Open a draft PR.
   - Title: `<ISSUE-KEY>: Upgrade <package> to <version> (<advisory identifier>)`.
   - Labels: this factory's label and `lane:security`. The code forge skill resolves the factory label and has the commands.
   - Description, using the repository's PR template when one exists, otherwise these sections:
     - Why: the advisory, with its identifier and link, and where the vulnerable version was resolved.
     - What: the exact version changes, any code changed to keep working, and the risk that remains.
     - Verification: what you ran and what it proved.
   - Ensure there is an attribution comment on the PR; the code forge skill has the check and the mechanics.
7. Record the PR on the issue. Leave the PR a draft: the review stage readies it when the review accepts it.

## Revisions

Review findings and CI failures come back here as revisions of the same PR. For each review finding, make the change or say in the thread why you did not, then reply in the thread and resolve it with the code forge skill's `resolve-threads` script. Verify again. Never open a second PR for the same advisory.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `pr`: the draft PR, with the advisory, the version changes, the residual risk, and the evidence in its description.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `code-quality`: the standard for any compatibility change. Read it before you write code.
- `factory-conventions`: the labels this PR carries and where it is recorded.
