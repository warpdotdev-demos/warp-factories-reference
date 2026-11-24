---
stage: fix
triggers:
    - provider: github
      event: workflow_run_completed
      filter:
        conclusions:
            - failure
            - timed_out
        labels:
            - lane:respond
        repos:
            - your-org/your-repo
        skip_bots: none
---
A workflow failed on a pull request from the respond lane. Diagnose and fix the failing check on the triggering pull request. Continue the existing work, in the responder conversation that owns the PR, at fix; do not confirm a new signal, and do not open a second pull request.

Act only on the latest result for the PR's current head commit. Ignore a failure on a superseded commit, and a failed attempt that is already rerunning or has since passed.

Keep the change inside the problem the PR exists to fix. If this same check has already failed after two fix attempts on this PR, stop: report the failure and what was tried, and wait for a human.
