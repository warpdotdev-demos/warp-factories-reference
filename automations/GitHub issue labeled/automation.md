---
triggers:
    - provider: github
      event: issue_labeled
      filter:
        labels:
            - factory:eng
        repos:
            - your-org/your-repo
    - provider: github
      event: issue_created
      filter:
        labels:
            - factory:eng
        repos:
            - your-org/your-repo
---

