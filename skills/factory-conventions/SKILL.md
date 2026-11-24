---
name: factory-conventions
description: "This factory's own conventions for the lanes added to the default blueprint: the labels that route pull requests and issues, who may set each one, and how to check the tracker for existing work before creating an issue. Read before you label a pull request or create an issue in an added lane. The code forge and tracker skills have the mechanics; this skill has the conventions."
---

# Factory conventions

These conventions belong to this factory, not to any one code repository, which is why they live here. Anything about how a codebase is built, tested, or laid out belongs in that repository's own agent instructions and skills.

## Pull request labels

Automations route follow-up events by these labels, so a missing or wrong label sends a CI failure or a review to the wrong stage.

- `factory:eng`: every pull request this factory opens or adopts. The code forge skill resolves it and has the commands.
- `lane:security`: a pull request from the security lane. The security worker sets it. Automations use it to send CI failures and review feedback back to `patch`.
- `lane:respond`: a pull request from the respond lane. The responder sets it. Automations use it to send CI failures and review feedback back to `fix`.
- A pull request from the default work track carries no lane label.
- `qa:passed`, `qa:failed`, `qa:not-reached`, and `risk:low`, `risk:medium`, `risk:high`: the result of the QA stage, which checks work-track pull requests. Only the QA worker sets them, and a pull request carries exactly one of each once QA has run.

Apply a lane label when you open the pull request, and never remove one.

## Tracker labels

- `factory-cve`: marks an issue in Jira project `ENG` as a dependency advisory for the security lane. The security lane adds it to the issues its audit files, and a person adds it to report an advisory. Never remove it.

Do not create other labels in the tracker.

## Existing work

Before you create an issue, search for one that already tracks the same work.

- Search open issues with distinctive terms from the signal, not generic ones:
  `project = ENG AND statusCategory != Done AND text ~ "<distinctive terms>"`
- When that finds nothing, run a second search with different terms before you conclude that there is no match.
- A lookup that failed is not the same as no match. Say that the search was unavailable, and do not create a parallel issue on that basis.

One issue tracks one piece of work. A second signal or a follow-up for the same work joins the existing issue as a comment.

## Where things go

- An issue you create: put the content in the description, and write a clear title.
- An issue a person filed: leave its title and description exactly as they wrote them. Add what you found as one comment, and edit that comment in place when it changes.
- Record the pull request on the issue as soon as it exists.

## Advisory issues

- Open advisories, oldest first:
  `project = ENG AND labels = factory-cve AND statusCategory != Done ORDER BY created ASC`
- An advisory has been assessed when it has a comment that begins with `Assessment:`. The assess stage writes that comment once and edits it in place if the outcome changes.
- For the audit's 30-day check on closed issues:
  `project = ENG AND labels = factory-cve AND text ~ "<package>" AND resolved >= -30d`
