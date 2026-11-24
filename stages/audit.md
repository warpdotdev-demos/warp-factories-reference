# Audit

This stage is executed by the security worker, inline in the `security` scope. Send questions and reports to the foreman for relay. Never declare inbox attention or complete the root task.

Scan the repository's dependencies for known advisories, file the ones that are new, and choose one to assess now. This stage does not assess or remediate; `assess` and `patch` do that.

## Repository instructions

Before you read, run, or change code, read the repository's own agent instructions for the files involved: a root `AGENTS.md`, `WARP.md`, or `CLAUDE.md`, and any directory-scoped equivalent deeper in the tree. Follow them, and use the repository's skills for build, test, and run commands. When the repository has neither, work from its README, build files, and CI configuration, and say in your report what you assumed.

## Procedure

1. Take the backlog first. Search the tracker for open issues labeled `factory-cve` that have no assessment recorded and no open PR (`factory-conventions`, Advisory issues). When there is one, choose the oldest as the advisory to assess now, skip the scan, and go to step 7. This keeps earlier findings from waiting behind new ones.
2. Find what to scan. List the dependency manifests and lockfiles on the default branch (for example `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `go.sum`, `Cargo.lock`, `poetry.lock`, `uv.lock`, `requirements.txt`, `Gemfile.lock`, `pom.xml`). A repository can have more than one ecosystem; scan each.
3. Scan each ecosystem with the audit tool the repository's instructions name. When they name none, use the ecosystem's standard tool when it is available (for example `npm audit`, `pnpm audit`, `govulncheck`, `cargo audit`, `pip-audit`, `bundler-audit`). Also read the repository's security alerts in the code forge when you have access to them. Do not install a tool by changing the repository. When no tool is available for an ecosystem, look up its resolved package versions against the OSV database, and say in your report which ecosystems you could not scan fully and why.
4. Keep the findings that matter: high and critical severity, for a version the lockfile actually resolves. A vulnerable range that no resolved version falls in is not a finding.
5. Group the findings by package. One issue covers every advisory for one package.
6. Skip a package that already has an open issue or an open PR, or an issue closed in the last 30 days (`factory-conventions`, Advisory issues). File at most three new packages in one run, worst severity first. For each, create one issue:
   - title `[<worst severity>] <package>: <n> advisories`;
   - every advisory identifier with its link, the affected and first patched versions, and the path by which the package is resolved (direct, or through which dependency);
   - label `factory-cve`.
   List the packages you left for a later run under `skipped`.
7. Choose the advisory to assess now: the backlog issue from step 1, or the worst of the issues you just filed. Report it with its issue. When the scan found nothing new and the backlog is empty, report `advisory: none` and post nothing.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `advisory`: the one advisory to assess now, with its issue, or `none`.
- `filed`: the issues created in this run.
- `skipped`: findings left out, each with the reason (already tracked, below the severity bar, over this run's limit, or an ecosystem that could not be scanned).

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `factory-conventions`: the `factory-cve` label, advisory issues, and the search for existing work.
