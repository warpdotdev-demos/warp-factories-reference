---
enabled: false
stage: fix
triggers:
    - provider: github
      event: pull_request_review_submitted
      filter:
        labels:
            - lane:respond
        repos:
            - your-org/your-repo
---
A person submitted a review on a pull request from the respond lane. Address the review comments on the triggering pull request: make the changes they ask for, answer what they ask about, and reply in the threads you handle. Continue the existing work, in the responder conversation that owns the PR, at fix; do not open a second pull request.

A comment that asks for more than the problem requires becomes a follow-up issue, not more of this PR. An approving review with nothing to act on needs no work. End silently; the merge decision stays with the human.
