---
name: ship-feature
description: Ship a feature through planning, implementation, PR, and a closed review loop.
argument-hint: "<feature request, issue, or spec>"
disable-model-invocation: true
---

# Ship Feature

Run a **closed loop**: discover → plan → implement → prove → review → repair. The main agent is the flight controller: it owns decisions, repository safety, evidence, PR state, and the loop. Subagents receive bounded roles; their confidence never substitutes for verification.

Use these default profiles:

| Role | Model | Effort |
|---|---|---|
| Planner | GPT-5.6 Sol | Medium |
| Implementer | GPT-5.6 Luna | High |
| Reviewer | GPT-5.6 Sol | Medium |

Treat profile names as configured model aliases. Ask the user before substituting a model, changing effort, or adding a role. A fresh reviewer is another iteration of the existing Reviewer role, not a new role. Never emulate an independent Reviewer in the main context.

Before dispatching a role, read the Common contract and that role's contract in [`ROLE-CONTRACTS.md`](ROLE-CONTRACTS.md); include both in the subagent prompt.

## 1. Establish the flight plan

Read the feature request, repository instructions, relevant source, tests, architecture, issue context, and delivery conventions. Preflight:

- Planner, Implementer, and independent Reviewer dispatch with the configured profiles
- writable Git state and exact intended base SHA
- push remote, authentication, PR create/update access, comment access, and CI visibility
- existing branches, worktrees, or PRs matching the issue, request, or head branch

If a required capability is missing, ask the user to approve a concrete fallback or stop. Preserve reviewer independence and repository safety in every fallback.

Classify delivery state before mutation: new work, resumable owned branch/PR, conflicting or foreign work, or already satisfied with no diff. **Owned** means created in this run, or an existing PR has the same authenticated author plus a `ship-feature` marker matching the request fingerprint, branch, and recorded base. Treat every other apparent match as ambiguous and ask before mutation. Resume exactly one owned branch/PR. For already-satisfied work, prove it against the base and hand back without manufacturing a commit or PR. Ask how to isolate conflicting, detached, dirty, ambiguous, or foreign work.

Build a short **decision ledger** containing:

- goal and observable acceptance criteria
- in-scope and out-of-scope behavior
- constraints and repository conventions
- identified consequential unknowns
- request fingerprint (canonical issue/spec URL, otherwise SHA-256 of the original request), ownership evidence, exact base SHA, baseline commands/results, and delivery route

Do the legwork before asking. Ask when an identified unknown can materially change product behavior, scope, architecture, data/privacy, migration, rollout, cost, destructive operations, or acceptance. Use the question tool for enumerable choices; otherwise ask one clear question. Ask dependent decisions one at a time, include a recommendation and consequences, and derive discoverable facts from the repository.

For new work, create a convention-compliant branch from the intended base. Record branch ownership. Preserve published history and push only fast-forward updates to the owned feature branch; request explicit approval for any history rewrite or transfer of branch/PR authority.

**Complete when:** capabilities and delivery state are classified, every acceptance criterion is observable, every identified consequential unknown is answered or explicitly user-approved, baseline evidence is tied to an exact base SHA, and the owned branch/PR state safely isolates or resumes the work.

## 2. Commission the plan

Dispatch one Planner with the feature request, decision ledger, repository context, and planner contract. Require repository-grounded output rather than a generic design.

Challenge the returned plan against the codebase. Reconcile incorrect assumptions yourself; return to the user only for consequential decisions under the question gate. Keep one canonical internal plan until PR creation.

**Complete when:** every acceptance criterion maps to implementation locations and planned proof; every quality lens is marked applicable or not applicable with evidence; applicable risks have mitigations; sequencing, migrations, rollout, and rollback are explicit where relevant; no unresolved design choice is being silently delegated to the Implementer.

## 3. Commission implementation

Dispatch one Implementer with the accepted plan, decision ledger, current base/head SHAs, relevant repository instructions, and Implementer contract. The Implementer may edit and test the owned branch/worktree. The main agent owns integration, commits, pushes, and PR lifecycle; transferring any authority uses the approval gate.

Inspect the resulting diff rather than trusting the report. Run focused checks throughout integration and the repository's required full validation before review. Verify generated files, migrations, lockfiles, API/schema changes, user-visible states, and operational configuration when present. Keep unrelated edits out of the branch. Observed evidence replaces planned proof in the acceptance map.

A clean integration state means the index and worktree contain only intentional feature changes, generated artifacts are current, and validation results are recorded against the current HEAD.

If implementation exposes a consequential product or architecture decision, use the question gate. If it exposes an ordinary defect or incomplete proof, send it back through the same Implementer role and continue the loop.

**Complete when:** the diff implements the accepted plan, every acceptance criterion has evidence, all applicable quality lenses in the role contract are accounted for, and required local checks pass from a clean integration state.

## 4. Open the evidence-bearing PR

Create coherent commits using repository conventions and fast-forward push the owned feature branch. Open one draft PR against the recorded base, or update the matching existing PR. Verify the remote base/head SHAs after every push. The PR body must include:

- a hidden `ship-feature` marker with request fingerprint, branch, and recorded base SHA
- problem and intended behavior
- scope boundaries and key decisions
- implementation summary
- acceptance-to-evidence checklist
- tests and manual verification performed
- the accepted quality-lens matrix from the Common contract
- known limitations or explicit `None`

Record failing external checks honestly with links or output and distinguish branch-caused failures from baseline failures.

**Complete when:** exactly one PR targets the recorded base and current head, its body contains every field above, and check states are recorded with their head SHA.

## 5. Close the review loop

For each round, dispatch a fresh Reviewer with the PR URL/number, base and head SHAs, decision ledger, accepted plan, validation evidence, and reviewer contract. The reviewer must inspect the actual diff and post one clear PR review or summary comment. It must avoid duplicating still-open findings from earlier rounds.

Collect the posted review and CI results yourself and validate each finding under the Common contract severity taxonomy. Send blockers and accepted improvements, with comment links and current base/head SHAs, through the Implementer role. Inspect the patch, rerun affected checks plus required full validation, commit, fast-forward push, update PR evidence, and dispatch a fresh review round. Reply to or resolve comments with evidence according to repository practice.

Evidence counts only for the exact base/head snapshot it evaluated. Discard stale reviews and checks after head changes; reassess material base drift before handoff. After the first complete review, accept a newly raised improvement only when it addresses code introduced since that review or is required by an acceptance criterion or repository standard; classify other late improvements as follow-ups. Retry flaky checks according to repository policy, or at most twice when no policy exists. Escalate when the same blocker survives two repair attempts, a repair round produces no relevant diff, or external checks remain pending beyond the repository's normal window.

Ask the user when a finding requires a consequential decision, a profile/role exception, an explicit repository-policy waiver, or no-progress escalation. Keep progressing on independent work before raising the question whenever possible.

**Complete when:** the latest exact-SHA Reviewer verdict has no blockers, accepted improvements are complete, every unresolved comment has a Common-contract classification, required checks for the final head pass or have a user-accepted baseline/external exception, the PR body matches the final diff, and the remote PR base/head match the verified snapshot.

## 6. Hand back control

Report the branch, PR URL, final base/head SHAs, merge-readiness verdict, checks and exact results, review rounds, resolved findings, remaining follow-ups, and user-accepted exceptions. Leave merging to the user unless they explicitly granted merge authority.

**Complete when:** every report field is present and the merge-readiness verdict is explicit.
