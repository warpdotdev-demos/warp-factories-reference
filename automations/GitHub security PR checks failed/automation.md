---
stage: patch
triggers:
    - provider: github
      event: workflow_run_completed
      filter:
        conclusions:
            - failure
            - timed_out
        labels:
            - lane:security
        repos:
            - your-org/your-repo
        skip_bots: none
---
A workflow failed on a pull request from the security lane. Diagnose and fix the failing check on the triggering pull request. Continue the existing work, in the security conversation that owns the PR, at patch; do not assess a new advisory, and do not open a second pull request.

Act only on the latest result for the PR's current head commit. Ignore a failure on a superseded commit, and a failed attempt that is already rerunning or has since passed.

A dependency upgrade that breaks the build often needs a small compatibility change; make only that. If this same check has already failed after two fix attempts on this PR, stop: report the failure and what was tried, and wait for a human.
