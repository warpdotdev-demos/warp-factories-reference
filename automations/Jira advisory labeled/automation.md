---
stage: assess
triggers:
    - provider: jira
      event: issue_labeled
      filter:
        labels:
            - factory-cve
        project_keys:
            - ENG
---
A Jira issue in `ENG` was labeled `factory-cve`. A person is reporting a dependency advisory. This enters the security lane at assess, with the issue as the advisory. Nobody has to assign the issue to the factory.

The triggering issue is the work item. Adopt it; never create a second issue for the same advisory, and never remove the `factory-cve` label. A repeat trigger with nothing new, or an issue the daily audit is already assessing, is a no-op.

Report the outcome on the issue. When the lane delivers a pull request, ask the person who labeled the issue for the merge decision. Never merge.
