---
name: pre-work-checks
description: "The two checks the worker runs before any new change, whichever stage it enters: existing-work discovery, and the scoping checklist of asks that the scoping gate puts to the user. Read before starting a new change outside triage, or when triage checks its scoping asks."
---

# Pre-work checks

The worker owns both checks below for every new change, including work too small for an issue or a spec and entries that skip full triage. The triage stage runs them as part of its procedure; a direct entry into spec or implementation runs them before creating a branch or PR. Reuse a recorded result on follow-ups unless the scope changes; do not repeat a completed check in each stage.

## Existing-work discovery

- Inspect supplied task, issue, and PR references first. An established owning task or PR is a continuation, not a reason to create parallel work. The current task itself is not a prior-work match.
- If those references do not establish an existing owner or delivered result for this scope, search the current Factory's task titles with distinctive terms using `list_tasks`, and search open PRs in the relevant repositories. Follow pagination for those searches and exclude the current task. Inspect plausible tasks and their linked artifacts for scope, ownership, and current state; similar titles or a task's stage alone do not establish a duplicate.
- If another task owns the same work, report the matching links to the foreman and stop before creating a branch, PR, or parallel issue. The foreman coordinates with that owner. If the work already landed, verify the delivered result rather than implementing it again.
- Record the search scope and terms plus the matching links and their disposition, or no match. Missing references in the brief are not evidence of a search. If a lookup is unavailable, record that limitation and report it to the foreman before creating parallel work; do not call it no match.

## Scoping checklist

Before a written spec or implementation, every open pre-implementation question is resolved with the user. These are the asks of the `scoping` gate on `triage`, `spec`, and `implement`; the blueprint says when it opens and what answers it. When triage ran, its research supplies the facts for these choices; on a direct entry, settle them before the stage's work begins.

- Change wanted: if the goal is unclear, ask whether a change is wanted at all.
- Approach: when reasonable alternatives differ in behavior, scope, risk, where the change lands, or cost, present the alternatives and your recommendation for the foreman to relay. Wait for the user's choice or explicit delegation; do not pick an approach on the user's behalf. Ask even for small work when the choice fits in a few lines.
- Spec: use the complexity assessed in triage to decide if a spec(ification), aka plan, is necessary. Specs are documents that outline the design and requirements for a complicated work item. They are especially useful when there is ambiguity that the user must help resolve. If there is no ambiguity and no decision that needs input from the user, do not make a spec and do not ask. If you think a spec is necessary, include the ask and explain concisely why.
- Clarifying questions: any open question of your own or one that triage surfaced.

Collect every open ask, and every one you can foresee, before raising `scoping`, so that the user answers once. When none are open, nothing opens; proceed. When triage itself needs an answer to continue - for example, information without which the problem cannot be identified - raise it then, together with the other asks you can already foresee, and carry the answers forward. A resolved ask stays resolved: do not re-open it on a later entry for the same work.
