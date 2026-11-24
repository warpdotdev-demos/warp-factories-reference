# Implement

This stage is executed by the worker. Send all user questions, approval requests, answers, and delivery messages to the foreman for relay. Never declare inbox attention or complete the root task.

The implementation stage makes the change that the work item describes, proves that the change works, delivers it as a draft PR, and decides whether that candidate needs the adversarial review stage. The stage does not triage, spec, or review the work.

Start from the work item, when the work is tracked: the issue seeds your context with the request, the findings from triage, the code locations, the reproduction, and the acceptance criteria. Start from the spec PR, when a spec exists: the committed spec is the contract for the change, and the change lands on that PR. Start also from what the conversation already established: the requester and the decisions already made.

## Preconditions

- Do not start while a scoping ask or the spec approval is still open. Apply this on every entry, including automation and feedback entries. Carry each still-valid answer in without asking again or extending its scope.
- On a direct entry that skipped triage, run both checks in the `pre-work-checks` skill first: existing-work discovery before creating any branch or PR, and the scoping checklist, whose open asks go to the user through this stage's `scoping` gate. Reuse recorded results on revisions unless the scope changes.
- When a relayed automation authorizes this entry (a CI failure or a submitted review), run only the bounded procedure that it authorizes; do not add artifacts or stages that it forbids.

## Outcome

- A PR that contains the change, its tests, and a description of what changed and how it was verified. When a spec PR exists, the change lands on that same PR and branch. Do not open a second PR for the same work.
- In hand when the stage ends: the PR reference, what changed, how you verified it, the links to your visual proof when you captured any (see the `ui-verification` skill), and the review decision below. Never merge the PR. A human merges.

## Procedure

1. Seed context. Read the issue and the spec, when they exist. The spec's validation criteria are your checklist. Read the applicable code, the package conventions, and the existing tests, so that the change follows the repository's patterns. Do not collect again what the issue and the spec already contain. Confirm that the existing-work discovery result is recorded before you create a branch or PR for a new change.
2. If you find a real ambiguity that the issue, the spec, and the code do not resolve, do not guess. Ask the foreman to relay the question to the user, and stop until the answer arrives. Batch your questions into as few rounds as possible. If the spec itself is wrong or incomplete, report the mismatch to the foreman. Do not silently diverge from the spec. If work that looked trivial turns out to need research, a tracked issue, or a reproduction, end the stage with `needs_triage: true` instead of continuing on a guess. If the user declines further work, end the stage and report the artifacts and obligations that remain open.
3. Implement. Before you write, read the repository's own agent-facing guidance for the code you are about to touch - a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree - and follow it. Make the minimal correct change. When a spec exists, implement what it describes. When there is no spec, make a targeted change at the root cause without unnecessary refactors. Commit your changes incrementally. Keep the diff clean: never commit scratch scripts, logs, screenshots, setup added only to exercise the change by hand, or other verification artifacts. Follow the codebase's existing style and conventions.
   - Write to the `code-quality` skill's standard; the review stage judges your change against that same file.
   - The work item, the triage findings, the review comments, and the spec explain how the code came to be. That belongs in the PR description and the commit message, never in the code or a test name.
4. Self-review. Read the full diff from start to end against the `code-quality` skill, as the review stage is about to. Confirm that each acceptance criterion and each spec criterion is met. When the change touches a concept that recurs elsewhere in the codebase - a value mirrored in a sibling map or config, a check duplicated at another call site, a validator with more than one caller - search for every other occurrence and confirm that each one is updated or deliberately left alone; a change that only reaches the first instance you found is incomplete. Remove debug code, scope creep, and any test-only setup added only to exercise the change by hand. List every comment line your diff adds. For each: is it intelligible without looking anything up - this PR, the review, a ticket, or an earlier revision - and does it tell the reader something the code doesn't already say? Delete any that fail either question, and leave the comments you did not write alone.
5. Verify the settled change. Verification is mandatory - see the Verification section below for details. Never deliver a change whose behavior you did not prove.
6. Deliver as a draft. Reuse the spec branch and PR when present, and rewrite the PR's title and description (see the PR description section below) to describe the implemented change rather than only the spec. Otherwise open a draft PR. The PR carries this factory's label either way - the code forge skill resolves the label and has the commands. Ensure there is an attribution comment on the PR, and post one if there is not - the code forge skill has the check and the mechanics. Record the PR reference on the issue when one exists. Leave the PR a draft until the review decision.
7. Review decision. Decide whether this candidate benefits from an adversarial review; see the Review decision section below. When the decision is `skip`, ready the PR for the user; see the Readying the PR section below. Then report the PR, the verification results, the remaining decisions, the visual-proof links, and the review decision to the foreman. Never complete the root task.

### Review decision

- The purpose of the review stage is a second, adversarial pass over the implementation to identify potential issues with it, whenever it changes. You wrote the diff, so the pass costs deliberate effort; the review stage says how to read it as if someone else had written it.
- Skip review only for changes whose full effect is obvious on sight: a copy or comment change, a flag flip, a config value tweak, or when the user explicitly asked to skip review. For a revision, judge this on the changes since the last review, not the change as a whole - a revision can be trivial while sitting on top of a large reviewed diff. Judge by the risk of the change, not by the line count. A one-line change to logic (for example a bug fix that alters a condition) can be subtle and still needs the review. When in doubt, review; it is the default.
- A revision skipped this way converges the loop the same as an `accepted` verdict: the PR goes to the user.

### Readying the PR

When the PR goes to the user without the review stage, ready it for a human merge decision:
- Verify that the PR is open and unmerged.
- Request the requester's review when the code forge allows it. A PR author cannot review their own PR: when the PR is attributed to the requester, request no reviewer. Request another reviewer only when explicitly authorized, using a verified login that is not the PR author's.
- Mark the draft ready for review so that CI starts. Do not wait for CI; report its current result accurately.

The foreman presents the readied PR and the self-review outcome to the user for a merge decision.

### Verification

- For a bug fix, prove the defect first. Start from the reproduction that triage recorded on the issue, if any. Do not build the reproduction again if the repro exists. When the issue has no reproduction or there is no issue being tracked, investigate the affected code path to trace the root cause. If you cannot figure it out, try to reproduce it manually.
- For a bug fix, add a regression test that fails before the change and passes after it, unless the change falls into an exempt category below. For a new feature, add tests that cover the new behavior and its edge cases, in proportion to what a defect there would cost - the same standard that `code-quality`'s Tests dimension judges you against, not a separate bar restated here. Do not add trivial, redundant, or unnecessary tests. Before you deliver, make a deliberate keep-or-delete pass over every test and case you wrote - a table row or a subtest is its own case: for each, ask what behavior change would make it fail and whether that failure would be a real defect that no other case you are keeping already catches. Keep only the cases that pass that bar; delete the rest, including near-duplicates that only vary an already-covered input or restate an unchanged flag-off path, and ones that only helped you work the change out. Delete with them any production seam - an injected collaborator, a wrapper, widened visibility, a one-line function extracted to be testable - that existed only to support a test you deleted. Some changes are exempt from testing: config-only changes, dependency or version bumps, constant or flag defaults, and pure data or copy changes.
- While implementing, use the smallest targeted compile or test command that gives useful feedback. Do not run broad formatting, linting, builds, or test suites after every edit or commit.
- Once implementation and self-review are complete, validate the settled change in this order:
  1. Run the relevant tests and fix the code until they pass.
  2. Run the relevant lint, typecheck, and build checks and fix their findings. Return to the affected tests only when a fix materially changes behavior.
  3. Run every applicable mutating formatter once, after all other code changes are complete.
- Do not rerun tests or lint after the final formatter, and do not run a full presubmit in addition to the checks above, unless the repository's agent guidance, the user, the issue, or an approved spec explicitly requires it. CI owns the uncommon failures outside the targeted local coverage.
- Any later source, test, manifest, generated-code, or configuration change creates a new candidate. Run the affected portion of the sequence again and finish with the applicable formatter. PR text, comments, labels, and other metadata do not invalidate code validation.
- Confirm that the original symptom is gone on the real path, if possible.
- For a change in a user interface, capture visual proof with the computer-use tool and attach it to the PR description - do not commit the media. Read the `ui-verification` skill for the capture standard and what counts as valid proof. Proof that shows a wrong path, an incomplete state, or a missing acceptance criterion counts as missing proof.
- When a spec exists, each of its validation criteria must pass before you deliver.
- If the toolchain is not available and you cannot verify, say so in the PR description and in your report. Never claim that an unverified change passed.

### PR description

Write the description for a reviewer who will read the diff next. The description states the problem and the net outcome. The diff is the inventory of files.

Write in the spirit of ASD-STE100 (Simplified Technical English): short sentences in the active voice, one idea per sentence, one meaning per word used consistently, and no vague qualifiers ("as needed", "appropriately", "etc."). Prefer bullets over long prose.

Hard limits:
- At most 8 lines of prose, not counting a required repository template's fixed headings.
- At most 3 bullets under What. Each bullet is one outcome or behavior change, never one file, function, or commit.
- Do not list files, symbols, or commits. Do not narrate the implementation sequence. Do not paste the work item, the spec, or the review thread.

Use the repository's or the organization's PR template when one exists. Look for it in the standard locations, e.g. for GitHub: `.github/PULL_REQUEST_TEMPLATE.md`, `.github/PULL_REQUEST_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md`, or the organization's `.github` repository. If the template has a Changes, Files, or similar inventory section, write outcomes there under the same 3-bullet cap; do not fill it with a file list. When no template exists, use this default structure:

```markdown
## Why
The problem or the request. One or two short sentences. Reference the work item.

## What
The net change in behavior or contract. At most three bullets.

## Verification
How you proved it: the tests, the checks, and the visual proof for a user-interface change.
```

Size the description to the change, not to the skeleton. Whichever structure applies - repo template or the default above - is a ceiling, not a quota to fill: keep every section the template requires, but answer a required one as tersely as the change allows instead of padding it out. Drop only a section the template marks optional, or one that genuinely does not apply to this change - never a required section just because it would repeat another one. A one-line fix earns a sentence or two per required section, not an essay; a large or subtle change still earns a description a reviewer can act on without opening the diff first.

## Revisions

- This stage is re-entered for revisions. Unambiguous review findings, a failed QA check, a review submitted on the PR, a CI failure, an answer to your question, or a new instruction from the user can bring the delivered work back here.
- Treat that as a revision of the delivered work, not as a new task. You keep the full context from the initial implementation. Do not collect it again.
- Make the revision on the same branch and PR. For each review finding, make the change, or say in the thread why you did not. Apply each unambiguous finding's own correction as the review wrote it; do not restate it as a generic "add a test for it" when the finding did not ask for one - a blanket instruction applied every round is how coverage outgrows what any single finding actually asked for. Verify again per the Verification section. Update the PR description.
- Re-run step 4's comment pass on the revised diff. Answering a review finding is exactly when a durable note about the discussion is tempting to leave in the code; that note belongs in the thread reply, not the diff.
- Close out the review threads. That is part of delivering the revision, not an extra. When a finding came from a review thread on the PR, reply in that thread and resolve it, with the `resolve-threads` script of the code forge skill, so that the reply points at the commit that addressed the finding. A finding that you deliberately did not address stays unresolved and gets a reply that says why. Leave open the findings that were posted for the user's judgment; those are the human's to close. Do not also post a summary comment recapping what was addressed - the per-thread replies are the complete record. See the code forge skill for the reply mechanics and for how concise a comment should be.
- Every revision goes through the Review decision above before the PR goes to the user.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `needs_triage`: `true` only when work that was trivial became more complex than originally thought and now needs triage before continuing; otherwise `false`.
- `review_decision`: `review` (the candidate benefits from an adversarial review; the default when in doubt) or `skip` (the change's full effect is obvious on sight, or the user explicitly asked to skip review).
- `change`: the draft PR and the issue reference, what changed or was revised, the validation results, and the visual-proof links.

## Skills

The first skill below carries the standard your work is held to; the rest describe checks and surfaces. Read a skill before your first operation on its surface, and use only the skills that the work needs. Resolve each skill's path from the available skills catalog.
- `code-quality`: the standard your change is held to, the same one the review stage judges against. Read it before you write code.
- `pre-work-checks`: existing-work discovery and the scoping checklist, for a direct entry into this stage.
- `ui-verification`: capturing visual proof of a user-facing change with the computer-use tool.
