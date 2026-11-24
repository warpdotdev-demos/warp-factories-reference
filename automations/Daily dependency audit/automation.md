---
stage: audit
triggers:
    - provider: schedule
      event: cron_fired
      schedule:
        cron: 0 6 * * *
---
Daily dependency audit for `your-org/your-repo`, at 06:00 UTC. This enters the security lane at audit.

The audit stage scans the repository's dependencies, files an issue for each newly affected package (at most three in one run), and takes one advisory into assessment in this same task. When nothing new is found and no earlier advisory is waiting, end without posting anything.

Nobody asked for this, so nobody is waiting on a reply. When the lane delivers a pull request, ask for the merge decision in `#engineering`. Never merge.
