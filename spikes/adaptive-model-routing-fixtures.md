# Adaptive Routing Fixtures

These paper fixtures test policy determinism rather than real model quality. Capability classes and families are assumed calibrated by the harness/repository. Each case names its bound and terminal state.

## Full harness inventory

Current profile is `steady`; provider `A` is approved. Provider `B` is approved only for public/internal data. Rates are fixture cost units for the same bounded dispatch.

| Profile | Provider | Family | Class | Logical effort | Cost | Latency | Isolation/tools/telemetry |
|---|---|---|---:|---|---:|---:|---|
| `swift` | A | S | C0 | low, fixed | 1 | 1 | yes |
| `steady` | A | T | C1 | low–high | 4 | 3 | yes |
| `deep-a` | A | U | C2 | medium–high | 12 | 8 | yes |
| `deep-b` | B | V | C2 | medium–high | 10 | 5 | yes |
| `inventory-only` | A | W | C2 | high | 0.5 | 1 | no isolated dispatch |

Unless a case says otherwise, the initial three-role estimate is 12 units and the 50% repair reserve makes the aggregate run budget 18. The autonomous dispatch cap is eight.

## Expected cases

### 1. Localized change with deterministic tests

Vector: localized, established, reversible, strong oracle, internal, normal urgency.

- minimum: C0 / low
- eligible: `swift`, `steady`
- balanced selection: `swift`
- user gate: none
- bounded path: initial → one retry → C1 escalation; no fourth attempt for the same gap
- terminal: criterion met, or user/no-progress gate within three dispatches

### 2. Cross-system feature with unclear ownership

Vector: cross-system, novel ownership decision, shared impact, mixed evidence, internal.

- minimum: C2 / high because cross-system architecture triggers C2
- eligible by capability: `deep-a`, `deep-b`; `inventory-only` fails isolation
- default provider/rate policy: neither is autonomously eligible (`deep-a` exceeds current rate; `deep-b` also needs provider approval)
- user gate: choose an expanded envelope/provider, reduce scope, or stop
- terminal: after approval, criterion met, explicit exception, or stop at budget/exhausted-set/no-progress gate; without approval, stop before paid work

### 3. Security- and migration-sensitive implementation

Vector: migration/data-integrity critical, security-sensitive, weak oracle, sensitive data.

- minimum: C2 / high
- eligible by data policy: `deep-a`; `deep-b` fails sensitive-data policy
- user gate: approve `deep-a` rate and aggregate budget, provide another approved profile, or stop
- bounded path after approval: initial → one retry → one escalation if a higher approved class exists
- terminal: criterion met, explicit exception, or stop at exhausted set/budget

### 4. Urgent repair where latency dominates price

Vector: deployment-critical, internal, blocking. Expanded policy approves both providers, 12-unit dispatches, and a 36-unit run budget.

- minimum: C2 / high
- rush Implementer: `deep-b` from observed latency
- C2 Reviewer family gate: `deep-b` is ineligible after family-V implementation; select `deep-a` (family U)
- user gate: none because provider, price, aggregate budget, and diversity are pre-approved
- bounded path: Implementer 10 + Reviewer 12 leaves 14 units; any repair must fit that balance and dispatch cap
- terminal: exact-SHA approval or budget/no-progress gate

### 5. Harness with models but no cost metadata

The current `steady` profile and another C1 profile have verified dispatch controls but unknown rates. No prior budget approval exists.

- minimum: C1 / medium
- autonomous eligible set: empty because aggregate paid spend cannot be enforced
- user gate: approve a token-bounded run, provide rates, pin a profile, or stop
- terminal: after approval, criterion met, explicit exception, or stop at budget/exhausted-set/no-progress gate; without approval, stop; unknown never becomes zero

### 6. Harness with no model-discovery capability

The harness can invoke its current model but cannot enumerate effective profiles or verify per-dispatch selection.

- effective profile set: empty
- user gate: approve the current verified isolated dispatch profile if the harness can expose it, configure candidates, or stop
- invalid fallback: treating main-context work as independent review
- terminal: after establishing a profile, criterion met, explicit exception, or stop at budget/exhausted-set/no-progress gate; otherwise orchestration stops before Planner dispatch

### 7. Repeated implementation failure requiring escalation

A C1 Implementer omits the same invariant.

- dispatch 1: `steady` / medium
- dispatch 2: focused retry at `steady` / medium with raw failure evidence
- dispatch 3: monotonic escalation to `steady` / high; if unsupported, next approved class
- de-escalation: blocked until this invariant closes
- budget: every call decrements both the 18-unit aggregate and eight-dispatch cap
- terminal: criterion met by dispatch 3, or user/no-progress gate

### 8. Late mechanical repair that can de-escalate

A C2 review blocker is fixed and verified; remaining work is a generated-file refresh with a strong oracle.

- new repair minimum: C0 / low; select `swift` if budget remains
- closed blocker retains its C2 evidence trail
- final Reviewer minimum: C2 and a family different from the final C2 Implementer where available
- bounded path: mechanical dispatch plus one exact-SHA Reviewer within remaining aggregate/dispatch budget
- terminal: exact-SHA approval or budget gate

## Constrained harness

One effective family-T C1 fixed-effort profile exists with known rates, token caps, telemetry, isolation, and tools.

- C0/C1 tasks may use it; fixed effort does not disqualify it
- C2 tasks trigger the user gate because the capability set is exhausted
- model switching is impossible; one focused retry then no-progress gate
- C2 diverse review is impossible and requires an explicit waiver or another profile

## Result

All eight scenarios specify minimum class, eligible set, selection or user gate, aggregate/dispatch behavior, escalation bound, and terminal state. They distinguish inventory from dispatch capability, calibrated quality from names, logical effort from provider controls, and optimization from policy approval.
