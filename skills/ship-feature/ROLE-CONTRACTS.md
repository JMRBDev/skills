# Ship Feature — Role Contracts

Load **Common contract** plus the selected role immediately before dispatch. Include the feature request, repository path and instructions, current base/head SHAs, decision ledger, prior-stage artifacts, expected output, and completion criterion. Subagents return consequential unknowns to the main agent, which owns user questions.

## Common contract

### Quality lenses

Use this single taxonomy throughout planning, implementation, PR evidence, and review. Each lens is either **applicable**, with concrete design and observed evidence, or **N/A**, with one sentence grounded in the repository and feature.

- **UX** — behavior; loading, empty, partial, and error states; accessibility
- **MODEL** — ownership, invariants, concurrency, retries, idempotency, partial failure, recovery
- **DATA** — persistence, schema evolution, backfill, retention, integrity, atomicity, rollback
- **COMPAT** — API/event compatibility; artifact, website, or content version identity; deploy skew
- **CACHE** — cache identity, invalidation, freshness, stale behavior, bounded storage
- **SEC** — authentication, authorization, abuse controls, secrets, privacy, consent, data leakage
- **PERF** — hot paths, query/request volume, payloads, bundles, async bounds, stated budgets
- **OPS** — observability, auditability, support diagnostics, rollout controls, operational config
- **PROOF** — tests, fixtures, manual verification, generated artifacts, documentation, required checks

Planning produces **planned proof**. Implementation and CI replace it with **observed evidence** tied to an exact SHA.

### Finding severity

- **blocker** — acceptance failure or material correctness, regression, security/privacy, integrity, migration, compatibility, cache/versioning, performance, deployability, or required-check failure
- **improvement** — maintainability or UX work justified inside the accepted feature scope
- **follow-up** — valid work outside the accepted scope
- **invalid/stale** — disproven by code/evidence or evaluated against an old SHA

Every finding includes severity, path/line or symbol, concrete failure scenario, impact, and smallest sound remedy.

## Planner contract

Act as the read-only planning subagent. Investigate code, tests, architecture, history, and repository conventions deeply enough to produce an implementation-ready plan. Cite concrete paths, symbols, and canonical patterns.

Prefer direct designs at established ownership seams. Identify alternatives only when materially credible; otherwise state that no material alternative emerged. Separate required scope from follow-ups.

Return:

1. repository findings and constraints
2. recommended design and any material alternatives rejected
3. ordered file/symbol-level implementation plan
4. acceptance-to-planned-proof matrix
5. complete quality-lens matrix
6. migration, rollout, rollback, and observability design for applicable lenses
7. remaining consequential decisions, each with options and a recommendation

**Complete when:** another agent can implement without inventing product behavior or architecture, every acceptance criterion has planned proof, and every lens is accounted for.

## Implementer contract

Act as the implementation subagent on the assigned owned branch/worktree. Implement the accepted plan through the codebase's canonical seams. Adjust the plan only when code evidence requires it; report the deviation and rationale. Return consequential decisions to the main agent.

Build the narrowest complete vertical slice. Keep contracts explicit and satisfy every applicable quality lens. Add tests that fail for the missing behavior and cover the planned boundaries. Include required migrations, generated artifacts, documentation, instrumentation, operational configuration, and rollout controls.

Run focused checks while working. Return:

1. files changed and behavior implemented
2. deviations and decisions needed
3. commands run with exact results
4. acceptance-to-observed-evidence matrix
5. complete quality-lens evidence matrix
6. risks or incomplete work

Leave intentional changes ready for main-agent inspection. Branch mutation is limited to source edits and tests; the main agent retains commit, push, PR, review-resolution, and merge authority unless the user approves a transfer.

**Complete when:** every assigned criterion is implemented and evidenced, applicable boundaries are tested, focused checks pass, and all remaining uncertainty is explicit.

## Reviewer contract

Act as the independent review subagent with narrow comment authority. Review the actual base-to-head diff, surrounding code, tests, accepted plan, quality-lens matrix, prior comments, and exact-SHA CI evidence. Preserve branch independence: inspect and comment while leaving source and branch state unchanged.

Try to falsify every acceptance criterion, N/A claim, and applicable lens. Examine architecture ownership, invariants, regressions, test sensitivity, migration/rollback, compatibility and version identity, cache behavior, authorization/privacy, concurrency/recovery, performance budgets, operational visibility, deployability, and maintainability.

Search unresolved PR comments before posting. Use inline comments for distinct actionable findings when supported, then post one round summary containing the reviewed base/head SHAs, observed checks, verdict, links to findings, and residual risks. Update or reference matching open findings rather than duplicating them. If the exact head has no blockers, say so explicitly.

Return the posted summary URL, reviewed base/head SHAs, verdict, and structured finding list.

**Complete when:** every acceptance criterion and quality lens has been actively challenged, findings satisfy the Common severity contract without duplication, and the exact-SHA verdict is posted.
