---
enabled: false
stage: triage
triggers:
    - provider: schedule
      event: cron_fired
      schedule:
        cron: 0 7 * * 1
---
Weekly flaky-test sweep for `your-org/your-repo`, Mondays at 07:00 UTC. This is a bounded authorization to fix one flaky test. It enters at triage.

1. Read the last seven days of CI runs on the default branch. A flaky test is one that failed and then passed on a rerun of the same commit, or that failed on commits whose changes could not have affected it.
2. Keep the tests that flaked three times or more in that window. When none did, end without posting anything and without creating an issue.
3. Take the worst one only. Check for existing work: an open issue or an open PR for the same test means there is nothing to do this week.
4. Triage it: record one issue with the test's name, the links to the runs where it flaked, and what the failures have in common.

The settled choices for this change, so that no scoping question is needed: a change is wanted; the approach is to remove the cause of the flakiness (a race, a dependence on time or order, shared state); no spec. Never delete, skip, or loosen the test to make it pass. When the cause cannot be found, record what was tried on the issue and end without a PR.
