# Spike: Adaptive Model Routing

## Question

Can `ship-feature` let its main orchestrator autonomously choose model and reasoning effort per dispatch while balancing quality, cost, latency, task complexity, risk, and roadblocks?

## Verdict

Yes, with a **constrained adaptive router** rather than unrestricted model choice or a fixed role-to-model table.

The correct objective is not “pick the best model.” It is: **pick the cheapest, fastest available candidate that is sufficiently capable for the current dispatch, then escalate from evidence.** This avoids paying frontier-model cost for mechanical work while keeping a defensible path for high-risk or ambiguous work.

## Decisions

1. Default to `adaptive-balanced`; retain economy, rush, quality, confirm, and pinned modes.
2. Discover harness inventory and verify isolated dispatch capability separately.
3. Route only against calibrated capability classes; inventory names alone never establish quality.
4. Apply a default safety envelope for provider, price, effort, dispatch count, and sensitive data.
5. Separate hard capability filtering from optimization. Context, tools, privacy, and risk are gates; price and latency rank candidates only after those gates pass.
6. Classify every dispatch, not every role. A Reviewer examining a copy change and a Reviewer examining a destructive migration should not receive the same profile merely because both are Reviewers.
7. Choose effort independently from model tier through harness-specific mappings.
8. Escalate monotonically from evidence within a bounded dispatch envelope.
9. Preserve a dispatch ledger so routing is inspectable and can improve from outcomes.
10. Ask users about policy boundaries, not routine dispatches.

## Why the alternatives lose

### Always ask the user

Predictable but slow, cognitively expensive, and poorly informed: the orchestrator usually has better access to current code, failures, context size, and role requirements. Keep this as `confirm` mode.

### Fixed role profiles

Simple but wasteful and brittle. Role labels are weak predictors of task difficulty, and model availability changes across harnesses.

### Let the orchestrator freely choose by model name

Appears adaptive but is not defensible. Names do not reliably encode price, latency, context, tool reliability, or quality. It also creates silent spend and provider/privacy surprises.

### One weighted “best model” score

Creates false precision, hides unknown metadata, and permits cheap advantages to compensate numerically for missing hard capabilities. Filtering then lexicographic selection is easier to audit.

## Harness discovery

A portable skill should request a harness-native inventory. In pi, `pi --list-models` exposes provider, model ID, context, output, reasoning, and image support. Pi model configuration can also contain pricing and supported thinking levels, but a generic agent may not receive all of that metadata through its tools.

Therefore:

- discovered facts remain facts;
- inventory entries become candidates only after isolated dispatch, enforcement, tools, and actual-model verification are established;
- capability classes come from harness/repository calibration or recorded outcomes;
- repository/user profile data may supply configured estimates;
- missing cost or latency stays unknown rather than becoming zero;
- absent effective profiles triggers one candidate-set question or a stop.

A future harness API returning structured model metadata and recent latency would materially improve this policy. Parsing human CLI output should be a compatibility adapter, not the conceptual interface.

## What this branch changes

- Adds `skills/ship-feature/MODEL-ROUTING.md` with routing modes, inventory discovery, task classification, capability bars, selection, escalation, user gates, and fallbacks.
- Replaces fixed default role assignments in `SKILL.md` with adaptive routing hooks.
- Keeps the former Sol/Luna assignments only as available fallback profiles.

## Validation questions

Before merging, exercise the policy against at least these scenarios:

1. localized change with deterministic tests
2. cross-system feature with unclear ownership
3. security- or migration-sensitive implementation
4. urgent repair where latency dominates price
5. harness with models but no cost metadata
6. harness with no model-discovery capability
7. repeated implementation failure requiring escalation
8. late repair that becomes mechanical and can de-escalate

Expected outcomes for all eight scenarios are recorded in [`adaptive-model-routing-fixtures.md`](adaptive-model-routing-fixtures.md), including eligible sets, effort, user gates, escalation bounds, and terminal states.

The policy is successful if independent readers derive the same minimum class and eligible set, explain selection from known evidence, avoid routine user interruption, and stop before crossing constraints.
