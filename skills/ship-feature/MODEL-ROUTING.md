# Adaptive Model Routing

The routing objective is **satisficing**: choose the cheapest, fastest available dispatch profile that clears a deterministic capability and risk bar. “Most powerful” is not the default, and model names are not capability evidence.

## Routing modes

- **adaptive-balanced** (default) — minimum sufficient capability class, then repository success evidence, known total price, and observed latency.
- **adaptive-economy** — lowest known total price among all sufficient profiles; minimum capability class and latency break ties.
- **adaptive-rush** — lowest observed latency; known total price breaks ties.
- **adaptive-quality** — highest calibrated capability class up to one class above the minimum; known total price breaks ties.
- **confirm** — compute the adaptive choice, then ask before every dispatch.
- **pinned** — use user-specified profiles.

The user may select a mode or tighter boundaries in the request. Otherwise use `adaptive-balanced` with the default safety envelope below and report it in the flight plan.

## Default safety envelope

In the absence of explicit user/repository routing policy:

- the current session provider is allowed; a new provider requires approval
- a candidate’s published input and output rates may not exceed the current model’s rates
- before the first dispatch, estimate bounded input/output tokens for the required Planner, Implementer, and Reviewer calls at eligible rates; the autonomous run budget is that monetary estimate plus a 50% repair reserve
- actual cumulative subagent cost may not cross that run budget; reserve enough for the initial three roles before retries
- unknown rates, unavailable usage telemetry, or unenforceable per-dispatch token caps require one explicit budget approval before paid dispatch
- `high` is the maximum autonomous logical effort; `xhigh` or `max` requires approval
- at most eight subagent dispatches may occur in a run without renewed approval
- one focused retry and one monotonic escalation are allowed per unresolved completion gap
- repository data classified sensitive stays on an explicitly approved provider

Keep capacity for Planner, Implementer, and Reviewer before spending the envelope on retries. Crossing any boundary uses the user gate.

## 1. Build effective dispatch profiles

Model inventory and dispatch capability are separate. Use a harness adapter that can establish:

1. available provider/model IDs and structural metadata
2. fresh isolated subagent creation
3. per-dispatch model and effort enforcement
4. required tool assignment
5. actual model/effort verification after dispatch
6. enforceable per-dispatch input/output bounds plus usage and cost telemetry
7. observed latency when exposed
8. provider/data-policy eligibility

A catalog entry becomes an **effective dispatch profile** only when items 2–5 are verified. An inventory-only model is not dispatchable.

Each effective profile records:

- provider and exact model ID
- normalized model family and evidence source
- calibrated capability class (`C0`, `C1`, or `C2`) and evidence source
- context/output limits and input/tool support
- logical-to-provider effort mapping, including `fixed` when effort is not adjustable
- known input/output/cache price and observed latency
- allowed data classification and provider constraints
- availability, authentication, and isolation support

Metadata is **known**, **configured estimate**, or **unknown**. Capability class comes only from a harness/repository profile, trusted evaluation, or recorded successful outcomes—not from the model name. Never probe paid models merely to benchmark them during a feature run.

If the harness cannot produce effective profiles, use user/repository-configured profiles. If none exist, ask once to approve the current model for all roles or provide a candidate set. Missing dispatch primitives stop subagent orchestration; a current-model fallback does not emulate isolation.

**Complete when:** every candidate is genuinely dispatchable and every field is known or explicitly unknown.

## 2. Classify the dispatch

Record a task vector before each dispatch:

- **scope** — localized, multi-module, cross-system
- **uncertainty** — established pattern, design choice, novel/underspecified
- **blast radius** — reversible/local, user-visible/shared, security/data/deployment critical
- **proof** — strong deterministic oracle, mixed evidence, weak oracle
- **context** — expected prompt, repository context, and output size
- **tools** — read-only analysis, edits/tests, PR/API interaction
- **urgency** — normal or blocking
- **data** — public, internal, sensitive/restricted

Map it to a minimum class:

- **C0** — localized and reversible, established pattern, strong oracle, public/internal data
- **C1** — default; multi-file work, meaningful design choice, user-visible/shared impact, or mixed evidence
- **C2** — any security/privacy, migration/data-integrity, concurrency, cross-system architecture, deployment-critical impact, sensitive data, or weak oracle

When signals disagree, use the highest triggered class. Role names do not set the class.

## 3. Set logical effort

Logical effort is portable intent; the harness adapter maps it to provider controls:

- **low** — C0 work
- **medium** — C1 work
- **high** — C2 work

A fixed-effort profile remains eligible when its calibrated class clears the bar. Effort adjustability is required only when the routing policy specifically needs escalation on the same model.

Increase effort one logical band when uncertainty or proof difficulty rises without changing capability class. Choose model class and effort independently.

## 4. Select deterministically

Eliminate profiles that fail any hard requirement: capability class, context/output size, tools/input, isolation, data/provider policy, safety envelope, or reviewer-diversity rule.

Apply the selected mode’s ordering. Estimate total price only when the required token and price inputs are known; otherwise compare published rates lexicographically (output, then input, then cache) without inventing token counts. Unknown optimization metadata loses a tie to known metadata but never counts as zero.

When two profiles remain indistinguishable, prefer in order:

1. a profile with successful outcomes for the same role and class in this repository
2. the currently proven profile
3. the repository-configured fallback
4. ask once rather than guess

Record:

```text
role/round · task vector · minimum class/effort · eligible set
rejections and hard requirement · selected profile and ordering evidence
safety-envelope balance · escalation trigger · actual model/effort/outcome/usage
```

## 5. Preserve review independence

Every Reviewer uses a fresh isolated context and read-only branch authority. That is mandatory independence.

A normalized family is the underlying model lineage, independent of provider aliases or gateways; it comes from harness metadata or repository configuration rather than name parsing. For C2 changes, family diversity from the Implementer is an additional hard requirement when a sufficient approved profile exists. If none exists, ask for a waiver or approval of another provider/profile. C0/C1 review may reuse the family when fresh isolation is verified.

## 6. Escalate monotonically

Escalate when a role completion criterion remains unmet after one focused retry, context/output truncates required work, actual tools/model differ from the requested profile, newly discovered risk raises the class, or concrete review/test evidence shows reasoning insufficiency.

For one unresolved gap:

1. retry once at the same profile with the concrete failure evidence
2. raise logical effort one band when supported
3. otherwise move to the next calibrated capability class
4. stop at the safety envelope or exhausted eligible set and use the user gate

Carry the failed attempt and raw evidence into the next dispatch. Escalation never moves downward while that gap remains open. Distinct later mechanical work may be routed independently at a lower class. Review remains at or above the final diff’s risk class.

Terminal outcomes are: completion criterion met, user-approved exception, or stopped with no-progress evidence. Repeated novel failures still count against the eight-dispatch envelope.

## User gates

Adaptive routing handles routine choices. Ask when:

- no effective profile clears hard requirements
- selection crosses dispatch count, effort, per-dispatch rate, aggregate run budget, provider, privacy, or latency policy
- C2 review diversity needs a waiver
- actual dispatch differs from the requested profile
- the user selected `confirm` or `pinned`

Questions include the recommendation, expected trade-off, remaining envelope, and consequence of declining.

## Configured fallback profiles

Fallbacks are recovery rather than primary routing and must still satisfy provider, data, isolation, availability, and either the price gate or explicit prior approval:

| Role | Model | Effort | Capability |
|---|---|---|---|
| Planner | GPT-5.6 Sol | Medium | configured by harness/repository |
| Implementer | GPT-5.6 Luna | High | configured by harness/repository |
| Reviewer | GPT-5.6 Sol | Medium | configured by harness/repository |

A fallback without a calibrated capability class is pinned legacy behavior, not an adaptive candidate.
