---
enabled: false
stage: patch
triggers:
    - provider: github
      event: pull_request_review_submitted
      filter:
        labels:
            - lane:security
        repos:
            - your-org/your-repo
---
A person submitted a review on a pull request from the security lane. Address the review comments on the triggering pull request: make the changes they ask for, answer what they ask about, and reply in the threads you handle. Continue the existing work, in the security conversation that owns the PR, at patch; do not open a second pull request.

An approving review with nothing to act on needs no work. End silently; the merge decision stays with the human.
