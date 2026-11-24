---
name: jira
description: "Mechanics for tracking factory work in Jira: reading, creating, and updating issues through the connected Jira MCP server, reporting live progress to the Rovo chat, managing workflow status, formatting descriptions, attaching artifacts, and resolving users. Use when the work item lives in Jira or the request arrived from a Jira issue or Rovo agent session."
---

# jira

Use the Jira issue as the durable work record. Report progress in issue comments. Use the Rovo chat status line, when the run has one, as an additional live surface while work continues.

This skill covers the mechanics of the surface and the factory's Jira write rules, not what to write on an issue.

## Tools

All Jira operations go through the connected Jira MCP server. Do not use a Jira CLI or raw REST when an MCP tool exists for the operation.

- Enumerate the server's tools before concluding that an operation is unavailable. A relevance-filtered listing is not a complete tool inventory.
- If an MCP call fails, report the blocker to the orchestrator. Do not invent a second Jira integration path.
- Jira description updates replace the complete value rather than patching it.
- Jira status changes use a `transition_id` returned by the issue's available `transitions[]`.
- Use `update_status` as additional best-effort progress on active Jira-source runs. It writes no Jira data and posts nothing on the issue; it replaces the run's shared status line, which a Rovo chat can show while polling the run. It does not replace issue comments.
- Use `finish_task` to end the run. Its summary is the requester's answer. You may also post a concise completion comment on the issue when useful; see Comments.

`update_status` accepts active Jira-source runs whether or not a Rovo chat has joined, subject to the integration's tool availability. Call it as progress is made. See Reporting status for what each error code means, and Comments for the issue-comment policy that applies to every Jira run.

## Writing ownership

- The foreman performs every Jira mutation: creating issues, updating descriptions and fields, transitioning status, applying labels, commenting, and linking artifacts.
- Triage, spec, implementation, and review agents are Jira read-only and do not call `update_status`. They report proposed mutations and progress to the foreman with the exact field changes, recommended status, artifact links, and reply text.
- Child progress belongs in the conversation and in messages to the foreman, not in Jira comments.

## Reading an issue

- Fetch an issue with `get_issue`, by its key (for example `PROJ-123`) or numeric ID; it does not accept a URL. Read the description, the comments, the labels, the status, and the assignee. The newest comments can change the meaning of the request, so read them before you act.
- Read the available `transitions[]` before changing status. Use the matching transition ID; do not invent a status or transition.

## Searching for issues

- Use `search_issues` with required `jql`. Jira receives the JQL unchanged. Optional `max_results` defaults to 20 and is capped at 50.
- Use `get_projects` to discover visible project keys and `lookup_account_id` to discover user account IDs by name or email.
- Fetch a search result with `get_issue` when you need its complete description, comments, or transitions. Extract the key from an issue URL before fetching it.

## Reporting status

Only the foreman calls `update_status`. It records additional best-effort shared progress for active Jira-source runs, not a replacement for issue comments. A Rovo chat polling the run can display this line:

- Pass a concise present-progress action phrase intended to be rendered after the fixed UI prefix `Warp is`. The phrase itself must begin with a capital letter and must omit `Warp is`. Good examples: `Re-reading REV-30 and its comments`, `Moving REV-30 to In Progress`. Do not use step counters like `step 1/5 — re-reading...`, generic filler like `Working on this Jira assignment`, full sentences, trailing punctuation, or already-prefixed `Warp is ...`. It is normalized to one line — control characters and whitespace collapse — and truncated at 500 runes, so keep it well under that limit.
- Each call overwrites the last. There is no history and no thread; send the current state of progress, not a log of steps.
- It never finishes the run. Call `finish_task` to end the run; a completion comment may also follow Comments when useful.
- `run_binding_mismatch` means this run has no Jira source. Stop calling `update_status` and carry on with no other behavior change. `run_not_active` means the run is already terminal, so the update is simply too late; drop it. `run_not_found`, `not_configured`, and `invalid_claims` are blockers — report them rather than inventing a fallback.

Call `finish_task` once when the work for this run ends. Its summary is what the requester receives: a Rovo reply for an A2A run; the server posts it as a comment for a legacy Jira-triggered run. You may also post a concise completion comment on the issue, threaded like any other milestone comment, when it is useful to issue watchers. Avoid duplicating the exact text the server will post for a legacy run when you can tell that applies; if uncertain, posting is acceptable.

## Status vs labels

- Status tracks progress. Set the workflow status so that it reflects where the work is now, and move it as the work moves.
- Discover the statuses from the issue. Before you change status, fetch the available `transitions[]` for that issue and choose the transition whose destination status is the closest semantic match to the current stage of the work. Jira statuses may not map one-to-one onto factory stages; pick the closest available status. Do not invent a status name or a transition ID, and do not assume that every project has the same statuses.
- Labels do not track progress. Labels are for categorization, routing, and intake filters, for example an area or a product. Never add, remove, or require a label to mean "triage finished", "spec finished", "implementation finished", or "review finished". Do not build a parallel pipeline out of labels. Do not use `triage-done`, `spec-done`, `impl-done`, `review-done`, or `blocked` as pipeline state.
- Do not remove labels that other tools or people set. When the categorization needs to change, add the label you need; adding never requires removing one that you do not own.

## Resolving users

When an assignee or reporter traces back to a Slack identity, whether you are creating the issue or updating one later, resolve the Slack profile first (see the `slack` skill), then resolve that identity to the matching Jira user; leave the field empty rather than guessing when no unique match exists.

Use `lookup_account_id` with a required name or email fragment in `query`. Optional `limit` defaults to 10 and is capped at 50. It returns active human users on the connected site with `account_id`, `display_name`, and `active`, not users assignable in a project, and requires Browse users and groups. Email matching depends on site privacy settings; an email is returned only when Jira exposes it. Select a unique match rather than guessing from multiple results.

Pass the returned `account_id` as `assignee_account_id` to `edit_issue` or use it for mentions. Jira rejects assignment when the user is not assignable in the issue's project; report that error rather than claiming the assignment succeeded. Use `unassign: true` to clear an assignee; do not combine it with `assignee_account_id`.

## Watching an issue

- Only the foreman changes watchers.
- Use `watch_issue` with `issue_key` and an explicit `account_id` from `lookup_account_id`. `action` defaults to `watch`; use `unwatch` to remove that watcher.
- Watching does not assign the user. Jira has no CC field. Notification delivery depends on the project's notification scheme; a successful watcher change does not guarantee delivery.

## Creating an issue

- Only the foreman creates issues.
- Use `create_issue` with a `project_key` and an `issue_type`. `get_projects` lists visible project keys, but no tool lists issue type names. Prefer an existing issue, then an explicit request value, then deployment configuration.
- If no project is configured, use the project supported by the request, thread, and issue links already in hand, and report the key used. Ask only when the evidence is genuinely ambiguous; if Jira rejects the create, report the attempted key instead of probing other projects.
- If no issue type is configured, use `Task` and report that choice. If Jira rejects it, report the failure instead of probing other types.
- Set the metadata in the same create flow when possible: status (via an available transition after create if needed), labels, priority, estimate, and links. See Status vs labels for how to choose the status.

## Descriptions

- Jira descriptions use wiki markup: `h2.` for sections, `h3.` for subsections, plain paragraphs for prose, `*` for bullets, and `**` for nested bullets.
- Use bullets for Acceptance criteria and Testing. Do not wrap sections in ordered or lettered lists.
- Do not link an issue description to its own issue.
- Keep status, estimate, and labels in their Jira fields rather than repeating them in the description.
- `edit_issue` replaces the description wholesale. Compose the complete canonical description, update it once, then re-read the issue and verify the stored value.

## Updating an issue

- Change only the fields the factory owns and preserve unrelated metadata, including any categorization or other labels already on the issue.
- Use `edit_issue` for summary, description, `add_labels`, `remove_labels`, and assignee changes. Provide at least one change. Label additions and removals preserve the rest of the label set.
- When the progress of the work changes, update the status. See Status vs labels.
- For every step transition, first verify the child's claimed artifacts. Then update the description and fields, transition status via the chosen transition ID, and record artifact links. Report progress with issue comments and, when a Rovo chat exists, also `update_status`. End the run with `finish_task`. A completion comment on the issue follows Comments. Advance only after all Jira writes succeed.

## Comments

As soon as work begins, post an initial issue comment stating that you are starting. Do this as the first Jira write. Continue with concise progress comments at later milestones: spec or PR opened, blocked, and done.

- Thread comments under Warp's root. Call `get_issue` and choose Warp's root comment whose body is `This task has been received and is starting work.` or the earliest comment carrying a Warp run link. If neither exists, use the agent's own earliest issue comment. Pass that ID as `parent_comment_id`.
- `get_issue` returns a flat `comments[]` without parent IDs; threading is write-only.
- Use `add_comment` with `comment_id` when new content supersedes rather than adds. Omit `parent_comment_id` when editing.
- Keep bodies concise. Do not put run, session, or artifact links in comment bodies. The server footer handles them.
- Call `finish_task` to end the run. You may also post a concise completion comment, threaded like any other milestone comment, when it is useful to issue watchers. Avoid duplicating the exact text the server will post for a legacy Jira-triggered run when you can tell that applies; if uncertain, posting is acceptable.

## Attaching a PR

- Verify a PR, spec PR, or review before recording it.
- Do not add run, session, factory, or artifact links to Jira comment bodies. The Jira MCP appends the server-owned links footer.
- Record the artifact link on the issue only when a structured field supports it. Do not post a comment solely to paste a PR URL; a milestone comment that a PR opened follows Comments.

## Reporting the issue as a run output

Call `report_external_reference` with `reference_type: "jira_issue"`, the issue's URL, and a short title.

- Use the `url` field returned by `create_issue` or `get_issue` verbatim. Never construct or guess an issue URL yourself: the site host is not derivable from the issue key, the project, or the site's display name, and a guessed host produces a broken link.
- Do this as soon as the issue exists in the run, whether the foreman created it or you adopted it. Without it, the issue is not recorded as a run output.
- Not a Jira mutation, so any agent holding the issue in its run can call it.
- Report a given URL once per run.
- Separate from `update_status` and `finish_task`, and from `report_pr` for pull requests.
- Never blocks the work: on failure, warn and carry on rather than aborting or retrying in a loop.
