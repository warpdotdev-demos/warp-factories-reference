---
enabled: false
stage: confirm
triggers:
    - provider: github
      event: workflow_run_completed
      filter:
        branches:
            - main
        conclusions:
            - failure
        repos:
            - your-org/your-repo
        skip_bots: self
---
A workflow failed on the default branch of `your-org/your-repo`. Something that was merged may have broken the build. This enters the respond lane at confirm, with the run link and the failing jobs as the signal.

Nobody asked for this, so nobody is waiting on a reply. When the lane delivers a pull request, post one message in `#engineering`: what broke, what caused it, and the PR that fixes it, and ask there for the merge decision. When confirm finds that the failure was a flaky test, has already recovered, or is already owned by another task, post nothing.
