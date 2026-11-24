---
enabled: false
stage: review
triggers:
    - provider: github
      event: pull_request_ready
      filter:
        labels:
            not_in:
                - factory:eng
        repos:
            - your-org/your-repo
---
A pull request that this factory did not open was opened ready for review, or was marked ready. Review it. This enters at review; do not triage and do not start an implementation.

The PR belongs to its author. Post every finding and change nothing: no pushes to the branch, no thread resolutions, no changes to its draft state, reviewers, or labels. Do not ask anyone for a merge decision.

A PR that already has a review from this factory for its current head commit needs nothing more: end silently. When the review is posted, the review on the PR is the whole result. Do not post a separate summary anywhere else.
