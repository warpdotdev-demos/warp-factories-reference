---
stage: implement
triggers:
    - provider: github
      event: workflow_run_completed
      filter:
        conclusions:
            - failure
            - timed_out
        labels:
            in:
                - factory:eng
            not_in:
                - lane:security
                - lane:respond
        repos:
            - your-org/your-repo
        skip_bots: none
---
A workflow failed on a pull request from the default work track. Diagnose and fix the failing check on the triggering pull request. Continue the existing factory work, in the conversation that owns the PR, at implement; do not triage a new item, and do not open a second pull request. If no task owns the PR, adopt the triggering pull request.

Act only on the latest result for the PR's current head commit. Ignore a failure on a superseded commit, and a failed attempt that is already rerunning or has since passed.

If this same check has already failed after two fix attempts on this PR, stop: report the failure and what was tried to the requester and wait for a human, instead of trying a third time.
