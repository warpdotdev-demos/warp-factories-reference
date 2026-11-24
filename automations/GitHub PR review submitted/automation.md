---
stage: implement
triggers:
    - provider: github
      event: pull_request_review_submitted
      filter:
        labels:
            in:
                - factory:eng
            not_in:
                - lane:security
        repos:
            - your-org/your-repo
---
Address the review comments submitted on the triggering pull request: implement what they ask for, answer what they ask about, and reply in the threads you handle. Continue the existing work — do not triage, and do not open a second pull request.
