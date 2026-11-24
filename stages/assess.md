# Assess

This stage is executed by the security worker, inline in the `security` scope. Send questions and reports to the foreman for relay. Never declare inbox attention or complete the root task.

Decide whether an advisory affects this repository, from the advisory and what the repository actually resolves. This stage takes the place of triage for the security lane: no reproduction, no spec, and no computer use.

Start from the advisory: the issue that carries it, or the advisory `audit` chose.

## Procedure

1. Check for existing work. Read the issue and its latest comments. Look for an assessment already recorded there, a linked PR, or an open PR labeled `lane:security` for the same advisory and package. Continue existing work instead of starting parallel work. A repeat trigger with nothing new is a no-op: stop without writing anything.
2. Mark the issue as in progress; the tracker skill has the mechanics.
3. Establish the advisory. Extract the identifier (CVE or GHSA), the package, the affected range, the severity, the advisory link, and the first patched version. Verify them against a primary source: the vendor advisory, the GitHub Advisory Database, or OSV. Never invent an identifier or a severity; record what you could not confirm.
4. Establish what the repository resolves. Read the manifests and the exact resolved versions in the lockfile. For a transitive dependency, find the direct dependency that brings it in, using the ecosystem's own tool for that (for example `npm ls`, `pnpm why`, `go mod why`, `cargo tree -i`). Account for overrides, pins, and more than one installed version of the same package.
5. Establish where the vulnerable version runs: in the shipped product, in a development or build tool only, in tests only, or not installed at all. A version match shows presence, not exploitability. Where it is practical, check whether the code uses the vulnerable function or meets the advisory's preconditions, and say which of the two you established.
6. Decide.
   - `affected`: a vulnerable version is resolved and a patched release exists. Uncertainty about exposure is not a reason to wait; state it.
   - `not_affected`: no resolved version is in the affected range, or the package is not installed. Give the resolved versions and paths as evidence.
   - `no_credible_fix`: no patched release exists, the upstream is abandoned, or the only fix is a rewrite or a breaking upgrade that needs a human decision. Give a next step, a proposed owner, and the condition under which the issue can close.
7. Record the assessment once, as one comment on the issue that begins with `Assessment:` and has at most four bullets: the outcome with the decisive version and path, the exposure, the patched version or mitigation, and the next action. The audit stage uses that opening word to tell which advisories still need assessing. Do not edit the title or description of an issue a person filed. A later run that changes the outcome edits that comment in place.
8. When the outcome is `affected`, write the brief for `patch`: the target versions, the compatibility changes to expect from the changelog, and the risk that remains after the upgrade.

## Outputs

Report these when the stage ends; the blueprint's edges read them.
- `outcome`: `affected`, `not_affected`, or `no_credible_fix`.
- `brief`: when affected, the target versions, the compatibility changes to expect, and the residual risk.

## Skills

Read a skill before your first operation on its surface. Resolve each skill's path from the available skills catalog.
- `factory-conventions`: advisory issues and where the assessment goes.
