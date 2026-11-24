# Confirm

This stage is executed by the responder, inline in the `respond` scope. Send questions and findings to the foreman for relay. Never declare inbox attention or complete the root task.

Make the fast decisions only: is the problem real, is it still happening, when did it start, and is another task already handling it. Finding the cause waits for `investigate`. Do not change code in this stage.

Start from the signal in its own words, with its links: a failed workflow run on the default branch, or a post in the incident channel.

## Repository instructions

Before you read, run, or change code, read the repository's own agent instructions for the files involved: a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree. Follow them, and use the repository's skills for build, test, and run commands. When the repository has neither, work from its README, build files, and CI configuration, and say in your report what you assumed.

## Procedure

1. Read the signal.
   - A failed workflow run: which jobs failed, on which commit, and the failing output. The code forge skill has how to read a run and its logs.
   - A post from a person: what they see, where, since when, and every link or attachment they gave.
2. Check whether another task already owns this problem. Search the tracker for an open issue about the same failing check or the same symptom (`factory-conventions`, Existing work), and search this factory's open PRs labeled `lane:respond`. When one exists, add this signal to that issue as a comment with its link, and report `confirmed: already_owned` with that issue.
3. Confirm that the problem is real and still present. Use the cheapest check that settles it.
   - For a failed run: look at the latest run of the same workflow on the default branch. A later green run on the same or a newer commit means it already recovered. A job that failed and then passed on a rerun of the same commit is a flaky test, not a broken build: report `confirmed: no` and name the test, so a person can pick it up.
   - For a person's report: reproduce it by the shortest route available, such as running the failing test, calling the code path, or reading the logs and errors the reporter attached. When confirming it needs access you do not have (a production system, telemetry, customer data), do not guess. Say what you checked, what you could not check, and what a person would need to look at.
4. Establish when it started, as far as the evidence shows: the first failing run and the last passing one, or the first report and the last time it was known to work. Record the commits at both ends when the signal is a build. This range is what `investigate` searches.
5. Record the problem. Create one issue, or adopt the one the signal named, with:
   - a title that says what is broken, for example "Build on main fails in the integration tests";
   - what people see, and who is affected;
   - the signal, with its link;
   - when it started and the last state known to be good.
   Do not edit the title or description of an issue a person filed; add a comment.
6. Report the outputs.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `confirmed`: `yes` when the problem is real and still present, `no` when it is not, or `already_owned` when another task is handling it. Give the evidence in every case.
- `started`: when it started and the last state known to be good, with the commits at both ends when they are known.
- `issue`: the issue that tracks this problem, created or adopted, or the open issue this signal joined.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `factory-conventions`: the search for existing work, and where things go on an issue.
