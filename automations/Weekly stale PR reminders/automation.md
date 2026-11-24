---
stage: ops
triggers:
    - provider: schedule
      event: cron_fired
      schedule:
        cron: 0 9 * * 1
---
Weekly reminder about factory pull requests that are waiting on a human, Mondays at 09:00 UTC. This is a reminder only; it authorizes no repair.

1. List the open pull requests in `your-org/your-repo` that carry `factory:eng`, are ready for review, and have had no activity from a person for seven days or more.
2. For each one, post a single comment on the PR that mentions its requested reviewer, or its requester when there is none: what the PR does in one sentence, its `qa:` and `risk:` labels when it has them, and that it is waiting for a merge decision.
3. Skip a PR that already has a reminder from this job in the last seven days.

When no PR qualifies, post nothing.
