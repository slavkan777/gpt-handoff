REQUEST_ID: REQ-2026-06-12-twincore-logistics-connector-factory-planning-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-planning
PROJECT: TwinCore
GATE: planning / product architecture
ACTIVE_REQUEST_PATH: TwinCore/logistics-connector-factory-planning-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/logistics-connector-factory-planning-v0.1/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
COMPLETED: 2026-06-12
COMPLETED_BY: claude

# TwinCore Logistics Connector Factory — MVP Planning Dossier v0.1

> Routing note: the request was relayed by Slava in-session. At execution time the gate-specific
> `ACTIVE_REQUEST.md` was not yet present in the bridge (checked against handoff repo tip `796ac38`);
> REQUEST_ID / PROJECT / GATE were verified against the relayed request text. No conflicting open
> request exists — the previous TwinCore request (`REQ-2026-06-04-twincore-main-review-v0-2`) is
> completed (`_BRIDGE/STATUS.json` state `REPORT_READY`). Writing the ACTIVE_REQUEST file itself is
> outside this task's write scope; left for Architect GPT to backfill.
>
> This is a PLANNING document. No code was written, no source repo touched, no AIKB updated.

## 1. Current State Restored From AIKB

Read before planning (fresh clones of `slavkan777/ai-kb` and `slavkan777/gpt-handoff`, same-day):
all nine `00_CONTROL_CENTER` bootstrap/protocol files from the request's READ FIRST list, the four
`01_PROJECTS/TwinCore/` files (`PROJECT_PROFILE.md`, `CURRENT_STATE.md`, `TASK_LEDGER.md`,
`CONTEXT_PACK/LATEST_CHAT_HANDOFF.md`), and the three TwinCore evidence paths
(`TwinCore/_BRIDGE/LATEST_REPORT.md`, `TwinCore/latest-report.md`, `TwinCore/main-review-v0.2/report.md`).

Official TwinCore state being respected:

- **Status:** `REVIEW_CONTEXT_REGISTERED`; **current gate:** audit / Igor-facing architecture discussion.
- **Source repo:** Azure DevOps `Twincore-framework`; reviewed branch `main`; reviewed commit `f6c0c84`.
- **Accepted conclusion:** TwinCore is a conventional layered .NET 8 framework with useful AI/LLM-oriented
  documentation and a good MVP foundation; reviewed `main` is **not yet proven** AI-native / agentic /
  meta-driven. This dossier does not change, and does not depend on changing, that conclusion.
- **Standing audit findings that matter for this plan** (from `main-review-v0.2/report.md`):
  zero automated tests; doc↔code drift; fragile name-based, assembly-scoped DI discovery
  (silent non-registration risk for new assemblies); dead/half-wired components; `Extensions/`
  opt-in module seam is the healthiest extension point.
- **Boundaries in force:** no source changes, no commit/push/PR, no Azure DevOps writes, no
  cleanup/delete/archive, no AIKB writes without explicit `зафиксируй`, global `_BRIDGE` not authoritative.

## 2. New Workstream Interpretation

**Logistics Connector Factory is a new workstream inside the existing TwinCore project context** —
not a new AIKB project, not a replacement of the review/discussion track, and **not yet durable AIKB
state** (it becomes durable only when Slava says `зафиксируй`).

Provenance of the idea, kept honest:

- Igor raised it in a call with Slava on 2026-06-11: pain of many provider integrations, NSwag output
  "not production ready", legacy SOAP providers, the wish for a small tool that turns a spec + docs
  into a client built "by our rules", and a shipment-portal demo dream. Igor's three explicit research
  questions: *what already exists on the market; how those tools work; where LLM calls can be minimized.*
- The call was cut short exactly at the field-mapping application topic. The **canonical model and the
  Mapping Workbench concept in this dossier are an elaboration by Slava + Architect GPT on top of that
  call** — they are consistent with where Igor was heading, but Igor has not yet confirmed them.
  Section 18 exists to close that loop.
- INFER (from audit evidence, not from the call): the framework's ruleset file is internally named
  "Allfreight Analyser Ruleset" — TwinCore was partly extracted from a freight-domain product, so
  carrier-integration pain is likely first-hand for Igor, and prior logistics artifacts may exist.
- The work-item ticket for this task could not be read in the current environment (work-item read
  access unavailable); if the ticket contains requirements beyond the call, they are not reflected here.

## 3. Product Goal

**Owner-friendly:** a factory that turns any carrier's API documentation into a working, verified
connector for our own logistics platform — in days instead of weeks — where parsing and code
generation are deterministic machinery, the LLM only helps understand the provider's semantics, and
a human approves every field mapping before anything is generated.

Короткая версия для разговора с Игорем (UA):

> Це не «згенерувати клієнта зі Swagger». Це фабрика конекторів: на вході — будь-який опис API
> перевізника (OpenAPI, схеми, документація, пізніше SOAP), на виході — перевірений C#-адаптер,
> який кладе відповіді провайдера в **нашу канонічну логістичну модель**. Парсинг і генерація —
> детерміновані, LLM — тільки там, де треба «зрозуміти сенс», людина затверджує мапінг на
> спеціальному екрані (Mapping Workbench). Перший MVP — тільки Rate Quoting / Transit Time.

The product value chain: provider-specific API chaos → canonical TwinCore logistics model →
semi-automatic mapping → human approval → generated, verified adapter.

**What this is NOT (positioning):**

- **Not a ShipEngine/EasyPost/Shippo clone.** Those are shipping *aggregators*: they sell one
  unified API in front of many carriers and take a margin on every shipment; integrating them means
  adopting *their* model and their pricing, and still writing one integration. We are building the
  **factory that builds our own connectors** into **our own canonical model** — an internal
  capability of the logistics platform, not a reselling middleman. An aggregator is, for us, just
  one more provider the factory can connect (one input format among many), not a competitor category
  we copy.
- **Not "Swagger → C# client" either.** Spec-to-client generators end where our value begins: at the
  semantic mapping of provider fields onto our domain model, reviewed by a human and verified by
  generated tests (§4, §10).

## 4. Problem Statement

A serious e-commerce / logistics platform needs tens of external integrations: payment providers per
country (global + local players), carriers (FedEx/UPS/USPS-class and local ones), and aggregators.
Every integration today means: a developer downloads documentation, deciphers it, writes a C# client
by hand, maps provider fields onto the platform's own entities, and tests against a sandbox.

Why this is expensive and stays expensive:

- **Heterogeneity.** Modern providers expose OpenAPI; older ones expose SOAP only; some expose
  REST + SOAP mixed, where individual features exist only on the SOAP side. Docs quality varies from
  machine-readable specs to PDF prose.
- **Existing generators stop too early.** NSwag-class tools produce DTOs and a mechanical client from
  a spec, but the output is not production-ready: no company conventions (DI, resilience, error
  handling, logging), no tests, and — the key gap — **no mapping to our own domain model**. For
  SOAP-only or docs-only providers they produce nothing usable at all.
- **The real cost center is semantics, not HTTP plumbing.** Which of the provider's five "charge"
  fields is *the* price? What does service code `FEDEX_GROUND` mean in our service taxonomy? Which
  date field is the delivery estimate? That semantic mapping work is what burns developer days, and
  it is exactly the part no spec-to-client generator addresses.
- **Multiplication.** Per-country provider sets multiply the integration count; every provider API
  version bump re-opens the work.

## 5. MVP Scope

**One vertical only: Rate Quoting / Transit Time.**

User inputs: origin address, destination address, package info (weight/dimensions/declared value).
Carrier returns (normalized): price, currency, service type/code, service name, transit time,
estimated delivery date, warnings/errors, optionally taxes/fees.

In scope for MVP:

1. Provider schema ingestion: OpenAPI / Swagger / JSON Schema + example payloads (see §9 tiers).
2. Rate-endpoint detection assistance and request/response schema tree extraction.
3. Canonical logistics model (quote-centric subset, §8) shipped as a versioned contract package.
4. Mapping Workbench flow with the six mapping statuses and human approval (§10).
5. Deterministic generation: typed DTOs, typed client, adapter implementing the canonical port,
   mapping profile artifact, test skeleton (§11).
6. Mock-first verification; sandbox verification where a free sandbox is available (§17).
7. One reference carrier end-to-end, then a second carrier as the repeatability proof (§17-A5).

Guardrail: **two carriers fully working beat five half-integrated.** MVP optimizes for proving the
factory loop, not for carrier count.

## 6. Explicit Non-Scope

Not in this MVP (deliberately): tracking, label purchase/printing, shipment creation/booking,
pickup scheduling, returns, customs/duties documentation, payments and payment-provider connectors
(the same factory idea may serve them later, but MVP is carriers/rate only), full TMS functionality,
order intake/lifecycle design, address validation as a service, carrier billing/reconciliation,
multi-tenant concerns, Azure deployment planning, production hardening claims, UI design beyond the
functional Workbench described in §10 (no mockups in this gate), full WSDL/SOAP automation (phase 2;
manual-assisted path only), any marketplace/aggregator-reselling positioning.

## 7. First User Scenario

Two personas; the **integration engineer is the primary MVP user**, the portal end-user is the
demo payoff.

**Integration engineer (Workbench user):**

1. Creates carrier "X" in the factory; uploads/points to its Rate API spec (or pastes docs/examples).
2. The deterministic parser builds the provider schema tree; the LLM suggests which endpoint is the
   rate-quoting one and annotates candidate price/service/transit fields; engineer confirms endpoint.
3. Workbench shows provider schema vs canonical model; deterministic auto-match fills the obvious
   pairs; LLM proposes the semantic ones (each with a rationale); engineer reviews each rule —
   accept / edit / manual / ignore — until the coverage gate is green (§10).
4. Engineer approves the MappingProfile; the generator emits DTOs + client + adapter + tests; compile
   and mapping-coverage checks run; the mock verification executes golden sample payloads.
5. Carrier "X" becomes available to the platform as a rate provider.

**Portal end-user (demo):** enters two addresses (e.g., Khmelnytskyi → Kyiv), a 1 kg parcel, and gets
a list of quote options across connected carriers — service name, price, currency, transit days,
estimated delivery date — sortable by price or speed; express options cost more, slow options cost
less. One carrier failing produces a warning entry, not an empty screen.

## 8. Canonical Logistics Model

Quote-centric minimal model. Principle: **business entities are thin and stable; integration
metadata entities carry the factory's complexity; raw payloads are audit-only.**

Core business entities (MVP-required):

| Entity | Key fields | Why it exists |
|---|---|---|
| `Address` | Country, City, PostalCode, Street, Region/State, Residential/Commercial flag | Both quote inputs; residential flag changes pricing at major carriers |
| `Package` | Weight + WeightUnit, L/W/H + DimensionUnit, DeclaredValue | Quote input; explicit units force deterministic unit-conversion rules instead of silent assumptions |
| `Carrier` | Id, Name, Code, IntegrationType (generated-adapter / aggregator / manual), IsActive | Registry of connected providers; toggling without redeploy |
| `ShipmentQuoteRequest` | OriginAddress, DestinationAddress, Package, RequestedDate, Currency, ServicePreferences | The canonical input every adapter receives — the request-side mapping target |
| `ShipmentQuoteOption` | CarrierId, ProviderServiceCode, ServiceName, Price (Amount+Currency as Money), EstimatedTransitDays, EstimatedDeliveryDate, IsAvailable, Warnings, ProviderRawReference | The canonical output — the response-side mapping target; `ProviderRawReference` links to audit storage without polluting the business object |

Thin references (present, not designed in MVP):

| Entity | MVP treatment |
|---|---|
| `Order` (Id, Number, CustomerId) | Anchor only, so a quote can belong to a real platform flow; order lifecycle out of scope |
| `Shipment` (Id, OrderId, addresses, PackageId, SelectedCarrierId, SelectedServiceCode, Status) | Records "user picked this option"; full shipment lifecycle (booking/tracking) out of scope |

Integration/mapping metadata (the factory's own model):

| Entity | Why it exists |
|---|---|
| `ProviderSchema` | Versioned snapshot of the parsed provider schema tree (format-agnostic, §9); mapping rules reference it; schema change ⇒ new version ⇒ re-review delta |
| `CarrierEndpoint` | Which provider endpoint serves rate quoting + protocol/auth profile reference (no secrets stored) |
| `MappingProfile` | Versioned, approvable set of rules for (carrier, endpoint, schema version); the unit of human approval and the generator's input |
| `MappingRule` | One field-level rule: source path → target path, transform (incl. unit/enum/date conversions), status (§10), provenance (deterministic / LLM-suggested / human), reviewer + timestamp |
| `IntegrationRun` | One verification or quote execution: status, timing, environment (mock/sandbox/live) |
| `IntegrationLog` | Events per run for diagnosis |
| `RawRequest` / `RawResponse` | **Audit/debug only** — retained short-term, never the business source of truth, excluded from business queries |

## 9. Provider Input Formats

All formats converge into **one normalized internal schema tree (`ProviderSchema`)** so the
Workbench, mapping rules, and generator never care about the source format. Ingestion tiers:

- **Tier 1 — MVP, fully deterministic:** OpenAPI 3.x / Swagger 2.0 (JSON/YAML), standalone JSON
  Schema, plus example request/response payloads (example-to-schema inference is deterministic,
  flagged lower-confidence).
- **Tier 2 — MVP, LLM-assisted:** human documentation (HTML/Markdown/PDF text) and code samples.
  The LLM drafts a schema/endpoint description; **everything LLM-derived enters the Workbench as
  "Needs review"** — it never silently becomes truth.
- **Tier 3 — phase 2:** WSDL/SOAP. Deterministic WSDL parsing is well-established in the .NET
  ecosystem, but envelope construction, WS-Security and provider quirks push full support past MVP.
  The `ProviderSchema` abstraction is designed now so SOAP slots in without reworking the Workbench
  or generator. Interim path for a SOAP-only carrier: manual schema entry from captured example
  envelopes (Tier 4 flow).
- **Tier 4 — worst case:** undocumented/legacy HTTP. Manual schema entry in the Workbench from
  captured traffic examples; the rest of the pipeline (mapping → generation → verification) works
  identically.

## 10. Mapping Workbench

The product's main screen — not a dashboard. Functionally (no UI design in this gate): provider
schema tree on one side, canonical model on the other, the rule list with statuses in the middle;
selecting a rule shows source path, target path, transform, provenance, LLM rationale when present,
and sample values pulled from example payloads.

Mapping statuses (exact semantics):

| Status | Meaning | Who sets it |
|---|---|---|
| `Auto-mapped` | Deterministic engine matched name/type/path with high confidence; bulk-approvable but human-visible | Engine |
| `Needs review` | LLM-suggested mapping, or low-confidence deterministic match; requires explicit human accept/edit | Engine/LLM |
| `Manual` | Human authored or rewrote the rule | Human |
| `Ignored` | Provider field deliberately unmapped (irrelevant/noise) — recorded so coverage distinguishes "ignored on purpose" from "missed" | Human |
| `Required missing` | A REQUIRED canonical field has no source; **blocks profile approval** unless explicitly waived with a reason | Engine |
| `Conflict` | Multiple candidate sources for one target, or contradictory transforms; must be resolved by human | Engine |

Review flow: ingest → deterministic auto-match → LLM gap suggestions (with rationale + confidence)
→ human resolves every non-auto rule → **coverage gate**: all REQUIRED canonical fields mapped or
explicitly waived, no `Conflict`, no unreviewed `Needs review` → human approves → MappingProfile
version frozen → generation unlocked. Every approval records who/when; every rule keeps provenance —
this is the audit trail that makes "semi-automatic with human approval" a real claim instead of a slogan.

First-class rule types beyond plain field copy: enum translation tables (provider service codes →
canonical service taxonomy), unit conversions (lb↔kg, in↔cm), date/format parsing (e.g. a provider
returning day-format strings for delivery commitments), array element selection (e.g. picking the
correct charge entry from a rated-details array), and constant/default injection.

Worked example (provider fields of a FedEx-shaped rate response — INFER from field naming; first
target carrier to be confirmed with Igor):

| Provider field | Canonical field | Likely status |
|---|---|---|
| `rateReplyDetails[].serviceType` | `QuoteOption.ProviderServiceCode` | Auto-mapped |
| `rateReplyDetails[].serviceName` | `QuoteOption.ServiceName` | Auto-mapped |
| `ratedShipmentDetails[].totalNetCharge.amount` | `QuoteOption.Price.Amount` | Needs review (which charge entry is "the" price — human confirms) |
| `ratedShipmentDetails[].totalNetCharge.currency` | `QuoteOption.Price.Currency` | Auto-mapped after the amount rule is approved |
| `commit.dateDetail.dayFormat` | `QuoteOption.EstimatedDeliveryDate` | Needs review (format transform) |
| `alerts[].message` | `QuoteOption.Warnings` | Auto-mapped (array → list) |

## 11. Generated Output

Per approved MappingProfile version, the deterministic generator emits:

1. **Typed DTOs** for the provider's request/response (from `ProviderSchema`).
2. **Typed thin client** — HTTP plumbing, serialization, auth hook (credentials injected from
   environment/secret store, never generated into code), provider error envelope handling.
3. **Adapter** implementing the canonical port — `IRateQuoteProvider.GetQuotesAsync(ShipmentQuoteRequest)
   → quote options` — whose body is generated *from the MappingProfile*: request-side mapping,
   response-side mapping, transforms, warning extraction.
4. **MappingProfile artifact** (versioned JSON/YAML) — the persistent source of truth; regeneration
   is repeatable and diffable (see §15, Option C).
5. **Test skeleton**: unit tests asserting golden sample payloads → expected canonical output;
   a mock-server smoke test using recorded/sample responses; an optional sandbox test configuration
   (keys via environment only, nothing committed).
6. **Generation gates**: compile check and mapping-coverage validation must pass before the adapter
   is considered produced.
7. **Convention pack** ("by our rules"): DI registration extension, resilience policy placeholders,
   structured logging. The concrete rule-pack (TwinCore conventions vs a clean standalone profile)
   is an open question for Igor (§18 Q3) — relevant because the audit showed TwinCore's name-based,
   assembly-scoped DI silently ignores services in new assemblies, and generated adapters will live
   in new assemblies; the generated DI extension must therefore be explicit, not convention-scanned.

## 12. Runtime Flow

1. Portal (or API consumer) submits origin/destination/package → canonical `ShipmentQuoteRequest`.
2. The quote orchestrator fans out to all `IsActive` carrier adapters in parallel with per-carrier
   timeouts.
3. Each generated adapter deterministically: maps the canonical request → provider request DTO →
   calls provider endpoint (live, sandbox, or mock depending on environment) → parses the response →
   maps to `ShipmentQuoteOption[]` (prices as Money, units normalized, dates parsed, provider codes
   translated through the approved enum tables, alerts → Warnings).
4. Orchestrator aggregates options, sorts (price/speed), and returns **partial results on partial
   failure** — a dead carrier contributes a warning entry, not an outage.
5. Each adapter call records an `IntegrationRun` + `IntegrationLog`; `RawRequest`/`RawResponse` are
   persisted only in audit/debug mode with limited retention.
6. **No LLM anywhere in this flow.** The runtime path is 100% deterministic generated code; LLM cost
   exists only at design time (§13). This is the direct answer to the "minimize LLM calls" requirement.

## 13. LLM vs Deterministic Responsibilities

| Concern | Owner |
|---|---|
| OpenAPI / JSON Schema parsing, schema tree extraction | Deterministic |
| WSDL parsing (phase 2) | Deterministic |
| DTO / typed client / adapter code generation | Deterministic |
| Mapping config storage, versioning, approval workflow | Deterministic |
| Compile check, test skeleton, mapping coverage validation | Deterministic |
| Runtime quote execution | Deterministic (generated code only) |
| Understanding provider docs; drafting schema from prose | LLM (design-time) |
| Finding the rate-quoting endpoint among many | LLM suggests → human confirms |
| Semantic field matching (price/service/transit candidates) | LLM suggests → Workbench review |
| Explaining enums and error codes | LLM (annotations in Workbench) |
| Detecting missing required fields / asking review questions | LLM assists → coverage gate decides |
| Final mapping approval | Human, always |

LLM-cost minimization tactics baked into the design: (1) zero LLM at runtime; (2) deterministic
auto-match runs first, LLM sees only the residual gaps; (3) LLM analyses are cached per
`ProviderSchema` version — re-run only on schema change, not per session; (4) suggestion tasks are
small-context and fit cheaper models; human review absorbs imperfection, so the system does not need
expensive high-certainty prompting.

## 14. TwinCore Fit

How this *can* fit TwinCore's architecture — without claiming any of it exists today:

- **Contract package**: the canonical logistics model + `IRateQuoteProvider` port ship as a small
  standalone package. Generated adapters depend on the contract only — they do not need the full
  framework, which keeps the factory decoupled from framework cleanup timelines.
- **Module seam**: TwinCore's `Extensions/` opt-in project model (the healthiest extension seam per
  the audit) is the natural shape for carrier adapters consumed by a TwinCore-based logistics portal:
  one adapter = one opt-in module.
- **Where the factory itself lives**: recommended for MVP — a **separate repository/solution**, not
  inside `Twincore-framework`. Reasons: the framework repo is read-only under current boundaries; the
  audit found cleanup debt (zero tests, DI fragility) that the factory should not inherit on day one;
  a standalone tool keeps the "minimal separate project" framing Igor used. Folding proven parts into
  the framework later is a separate, explicit decision.
- **Known landmines if adapters target TwinCore conventions now** (from the accepted audit):
  name-based DI discovery is scoped to fixed assemblies — generated adapters in new assemblies would
  be silently not registered, so generated code must use explicit DI registration extensions; the
  framework has no test projects, while the factory's value proposition includes generated tests —
  the factory therefore carries its own test harness; `ServiceResponse`-style conventions can be part
  of the convention pack only after Igor confirms which conventions are canonical (the audit found
  doc↔code drift exactly there).
- **Strategic upside for Igor**: a generated, tested, convention-clean carrier adapter would make a
  strong candidate for the "golden reference feature" the audit said the framework currently lacks —
  and the factory direction is a concrete, evidence-backed step toward the AI-assisted vision for
  TwinCore, without overclaiming that reviewed `main` is already AI-native (it is not proven to be).

## 15. Architecture Options

**A. Runtime mapping config (interpreter).** A generic adapter engine reads the MappingProfile at
runtime and executes mappings dynamically.
*Pros:* no codegen step; mapping fixes apply without rebuild. *Cons:* opaque debugging (stack traces
point into the engine, not into carrier-specific code); weaker compile-time guarantees; runtime
failure surface for mapping errors; harder to hand a reviewer "the FedEx client" as readable code;
does not deliver the explicitly requested production-ready C# client.

**B. Generated C# adapter only.** Codegen from the mapping decisions; generated code is the only
artifact, the profile is throwaway.
*Pros:* typed, reviewable, debuggable, testable code per carrier. *Cons:* without a persistent
profile, every mapping fix means re-deriving decisions; no diffable semantic history; regeneration
after provider schema change loses the human review trail.

**C. Hybrid — persistent MappingProfile + generated adapter (RECOMMENDED for MVP).** The versioned,
human-approved MappingProfile is the durable source of truth; a deterministic, idempotent generator
emits the typed C# adapter from it; profile change ⇒ regenerate ⇒ diffable code change.
*Pros:* human-auditable semantic layer **and** production-grade typed code; repeatable generation;
re-review after provider schema bumps touches only the changed rules; matches the formula
"parser + generator deterministic, LLM for semantic gaps, human approval".
*Cons:* the generator must stay hermetic and idempotent (no hand edits inside generated regions) —
an accepted engineering constraint.

## 16. Risks

| # | Risk | Why real | Mitigation |
|---|---|---|---|
| 1 | **Scope creep toward a TMS** (tracking, labels, booking pull immediately) | The demo dream is a full portal; quoting is just the first slice | Rate-quote-only acceptance criteria (§17); non-scope list (§6) agreed with Igor up front |
| 2 | **Overclaiming AI** ("AI does the integration") | Sales-friendly but false; the audit already corrected one overclaim direction for TwinCore | Honest formula everywhere: deterministic core, LLM = design-time assistant, human approval; runtime has zero LLM |
| 3 | **Mapping ambiguity** (which charge is *the* price; gross/net/list; date day-formats; multi-entry arrays) | Real-world rate responses are semantically messy | `Conflict` / `Needs review` statuses, golden sample payloads as test fixtures, human gate before generation |
| 4 | **Provider auth/sandbox complexity** (OAuth flows, certs, sandbox account approval queues) | Big carriers gate sandboxes behind registration | Mock-first verification as the MVP gate; sandbox as a stretch criterion; no real keys in any artifact |
| 5 | **Dirty framework foundation** if adapters bind to TwinCore conventions now | Audit: zero tests, DI silent non-registration for new assemblies, doc drift | Standalone contract package for MVP; explicit DI registration in generated code; factory ships its own test harness |
| 6 | **Spec quality variance** (broken/partial OpenAPI in the wild) | Common with carrier APIs | Schema repair path through Workbench (Tier 2/4 ingestion); example-payload inference |
| 7 | **LLM suggestion errors** (hallucinated field semantics) | Inherent | Provenance + confidence on every suggestion; nothing LLM-derived is auto-approved; coverage gate |
| 8 | **Unvalidated elaboration** — canonical model + Workbench not yet confirmed by Igor (call cut off) | Building before validation wastes the MVP | §18 questions first; planning artifact only at this gate |
| 9 | **Unread ticket** — work item may contain requirements beyond the call | Access unavailable in this environment | Obtain ticket text via Slava before the design gate |
| 10 | **No test culture in the target framework** | Generated test skeletons only pay off if they run somewhere | Factory repo carries its own CI-able test project from day one |

## 17. Acceptance Criteria

MVP is accepted when each item below has its evidence tuple (command + exit code / artifact / log):

- **A1 — Ingestion:** a real carrier rate-API spec (OpenAPI/JSON) ingests into a browsable
  `ProviderSchema` tree without manual file surgery.
- **A2 — Workbench gate:** the six example mappings of §10 are resolvable in the Workbench; every
  REQUIRED canonical field ends `Auto-mapped`/`Manual`/accepted-`Needs review`, or is explicitly
  waived; profile approval is blocked while any `Required missing`/`Conflict`/unreviewed rule exists
  (negative test included).
- **A3 — Generation:** generated solution compiles (exit 0); generated unit tests pass against
  golden sample payloads (N/N).
- **A4 — Mock run end-to-end:** a `ShipmentQuoteRequest` (two addresses + package) through the
  generated adapter against a recorded mock returns ≥1 normalized `ShipmentQuoteOption` with price,
  currency, service code/name, and delivery estimate populated.
- **A5 — Factory repeatability (the core proof):** a second carrier is integrated by repeating the
  flow **with zero changes to factory code** — only a new profile + newly generated artifacts.
- **A6 — Partial failure:** with one carrier's mock forced to fail, the quote response still returns
  the other carrier's options plus a warning entry (not an error, not an empty result).
- **A7 — Zero runtime LLM:** no LLM call appears in the runtime quote path (verifiable by code
  inspection of generated adapters + absence of LLM traffic in runtime logs).
- **A8 — Secret hygiene:** no credentials in generated code, profiles, configs, or this report's
  successor artifacts; sandbox keys only via environment injection.

## 18. Questions For Igor

(EN for the record; UA phrasing for the actual conversation.)

1. **Where should the factory live** — a separate repo/tool (recommended for MVP) or inside
   Twincore-framework? / Де живе фабрика — окремий репозиторій/тулза чи всередині фреймворку?
2. **First target carrier?** The working example's field shapes match a FedEx-style rate API (INFER);
   alternatively a local-market carrier with an easier sandbox. / Який перевізник перший — FedEx-клас
   чи локальний з простішим сендбоксом?
3. **"By our rules" = which rules exactly** — TwinCore conventions (`ServiceResponse`,
   `AppServiceBase`, …) or a clean standalone convention pack for generated code? / «По наших
   правилах» — це конвенції TwinCore чи чистий окремий стандарт для згенерованого коду?
4. **Who uses the Mapping Workbench** — developers only, or also a non-dev integrator/analyst? This
   decides UI depth. / Хто користувач Workbench — лише розробник чи й не-розробник?
5. **How urgent is SOAP** — which real legacy provider should drive phase 2? / Наскільки терміновий
   SOAP і який реальний legacy-провайдер перший?
6. **The mapping application from the cut-off call** — is the Workbench described here what you
   meant, or did you envision something different? / Та «аплікуха для мапінгу», на якій обірвався
   дзвінок — це воно (Workbench) чи ти бачив інакше?
7. **Ticket contents** — does the work item contain requirements beyond the call? (Also: read access
   to work items for the executor.) / Чи є в тікеті деталі понад дзвінок?
8. **LLM constraints** — allowed provider(s), budget ceiling, any data-privacy constraints on sending
   provider docs to an LLM? / Який LLM-провайдер дозволений, бюджет, обмеження на відправку
   документації провайдерів?
9. **Acceptance demo** — what exactly do you want to SEE to call MVP done (how many carriers, which
   fields, mock or sandbox)? / Що саме ти хочеш побачити, щоб сказати «працює»?
10. **Product ambition** — internal accelerator for TwinCore-based projects, or potentially a
    sellable product? Affects licensing/architecture choices. / Це внутрішній прискорювач чи
    потенційний продукт на продаж?

Carry-over from the standing audit discussion (one conversation, two agendas): where do the
mentioned agents / meta-driven parts live (branch? future phase?); test strategy for the framework;
which README is canonical.

## 19. Recommended Next Gate

After Architect-GPT audit of this dossier + Slava's acceptance + the Igor validation conversation
(§18), the recommended sequence:

1. **`TWINCORE_CONNECTOR_FACTORY_MARKET_RESEARCH_V0_1`** (next safe gate, read-only): an
   evidence-based answer to Igor's three explicit questions — what exists (NSwag/Kiota/
   openapi-generator class tools, aggregators as a *different* category, any AI-mapping products),
   how they work, and where LLM calls are minimized in comparable systems. Cheap, directly requested
   by the owner, and it de-risks the design. A preliminary internal scan exists from a prior session
   but is not yet saved as evidence — this gate would formalize it.
2. Then **`TWINCORE_CONNECTOR_FACTORY_TECH_DESIGN_V0_1`** (planning, no product code): canonical
   contract package spec, `ProviderSchema`/`MappingProfile` formats, generator pipeline design,
   Workbench data flow — the blueprint the first implementation gate would build from.
3. Only after both: a bounded PoC implementation gate (separate explicit authorization, separate
   repo per §14 recommendation).

## 20. Not Touched

- **Twincore-framework source repo**: not read beyond previously accepted audit evidence, not
  modified; no commit, no push, no PR, no branch operations; no Azure DevOps write actions.
- **AIKB (`slavkan777/ai-kb`)**: read-only; no updates (`AIKB_UPDATE_REQUIRED: no`); the new
  workstream remains non-durable until Slava says `зафиксируй`.
- **Global `gpt-handoff/_BRIDGE/`**: not used as authority, not written.
- **`TwinCore/_BRIDGE/ACTIVE_REQUEST.md`**: left as-is (still the completed audit request) — outside
  this task's write scope; gate-specific ACTIVE_REQUEST backfill is for Architect GPT.
- No code generated, no DB migrations, no UI mockups, no secrets/API keys anywhere, no paid provider
  usage, no production-readiness claims, no claim that reviewed `main` is AI-native/agentic/meta-driven.
- Writes performed: exactly this report to the five authorized gpt-handoff paths (gate report,
  latest-report mirror, `_BRIDGE/LATEST_REPORT.md` mirror, `latest-summary.json`, `_BRIDGE/STATUS.json`).

## 21. Next Safe Step

Slava reviews this dossier and triggers `отчёт` for Architect-GPT audit; in parallel, takes §18 to
Igor. On acceptance: `зафиксируй` to register the workstream in AIKB, then `бриф` for the market
research gate (§19.1). No implementation before those gates.
