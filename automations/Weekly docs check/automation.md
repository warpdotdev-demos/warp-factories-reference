---
stage: implement
triggers:
    - provider: schedule
      event: cron_fired
      schedule:
        cron: 0 8 * * 1
---
Weekly documentation check for `your-org/your-repo`, Mondays at 08:00 UTC. This is a bounded authorization to correct documentation that the last week of changes made wrong. It enters at implement.

1. List the changes merged to the default branch in the last seven days.
2. Compare them with the repository's README and its documentation directory. Look for what those changes made untrue: commands, flags, configuration keys, API routes, file paths, and setup steps.
3. When nothing is wrong, end without opening a PR and without posting anything.
4. Otherwise correct the documentation in one PR. When an open PR from an earlier run of this job exists, update that PR instead of opening another.

The settled choices for this change, so that no scoping question is needed: a change is wanted; documentation files only; no spec and no issue. Do not change code, and do not rewrite documentation that is still correct. When a document cannot be corrected without a decision about what the product should do, leave it and say so in the PR description.
