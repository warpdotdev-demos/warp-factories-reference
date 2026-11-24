---
enabled: false
stage: ops
triggers:
    - provider: schedule
      event: cron_fired
      schedule:
        cron: 30 9 * * 1
---
Weekly digest of what the factory did, Mondays at 09:30 UTC. This is a report only; it authorizes no repair.

Gather the last seven days from this factory's tasks, its issues in `ENG`, and its pull requests in `your-org/your-repo`, then post one message in `#engineering`:
- how many requests arrived, and how many ended in a merged PR, an answer, or a declined request;
- the pull requests merged, each as a link with one phrase;
- the pull requests still waiting on a human, each as a link with its `risk:` label when it has one;
- anything from the added lanes: problems confirmed, advisories assessed, and what is still open.

Keep it to what a reader can act on. Leave out a line whose count is zero, and when nothing happened at all, post nothing.
