---
triggers:
    - provider: github
      event: issue_mentioned
      filter:
        labels:
            - factory:eng
        mentioned:
            - warp
            - warp-factory
        repos:
            - your-org/your-repo
    - provider: github
      event: pull_request_mentioned
      filter:
        labels:
            - factory:eng
        mentioned:
            - warp
            - warp-factory
        repos:
            - your-org/your-repo
    - provider: github
      event: issue_assigned
      filter:
        assignees:
            - warp
            - warp-factory
        labels:
            - factory:eng
        repos:
            - your-org/your-repo
    - provider: github
      event: pull_request_assigned
      filter:
        assignees:
            - warp
            - warp-factory
        labels:
            - factory:eng
        repos:
            - your-org/your-repo
---

