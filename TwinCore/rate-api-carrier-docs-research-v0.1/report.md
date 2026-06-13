# TwinCore Rate API — Carrier & Shipping API Docs Research v0.1

REQUEST_ID: REQ-2026-06-14-twincore-rate-api-carrier-docs-research-v0-1
PROJECT: TwinCore
GATE: research / specification support
STATUS: READY_FOR_AUDIT
TASK_TYPE: research-spike
DATE: 2026-06-14
AUTHOR: Claude (researcher); research/report only — no code, no AIKB writes, no branches, no live carrier calls
RESULT: RESEARCH_DONE_READY_FOR_AUDIT

---

## 1. Request identity

| Field | Value |
|---|---|
| Request | `REQ-2026-06-14-twincore-rate-api-carrier-docs-research-v0-1` |
| Gate ACTIVE_REQUEST | `TwinCore/rate-api-carrier-docs-research-v0.1/ACTIVE_REQUEST.md` (read in full) |
| Bridge ACTIVE_REQUEST | `TwinCore/_BRIDGE/ACTIVE_REQUEST.md` — same REQUEST_ID; router confirms |
| Routing evidence | gpt-handoff commit `18c7ecd` "Route active request to carrier docs research gate" |
| AIKB context read | 11 READ-FIRST files incl. SDD package `SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/` (ai-kb @ `ae85405`) |
| Identity note | bridge `STATUS.json` still pointed at the prior blueprint-review gate when this gate opened; the two ACTIVE_REQUEST files + the routing commit agree, so this report proceeds. STATUS.json is updated on publish (preserving the two pending audits for G1 + blueprint review). |
| Decision mode | owner-proxy (Slava/GPT) while Igor unavailable; all findings advisory, reversible. |

## 2. Method and limitations

- **Method:** public-internet research via search + page fetch against **official developer portals first**; aggregator findings cross-checked against the providers' own docs. Five parallel research passes (big global carriers / EU posts / APAC+ME / NA regionals / aggregators), then synthesis.
- **Evidence labelling:** every cell is graded `official-verified` (read on the provider's own docs/spec), `third-party` (Postman public network, RapidAPI, GitHub mirrors, aggregator statements), or `model-knowledge-only` (not freshly verifiable). Where neither official nor confident, the provider is marked **NEEDS_MANUAL_CHECK** rather than guessed.
- **No facts invented.** No credentials used, no paid signup, no live/sandbox carrier call made. A WSDL URL is reported as "exists/reachable" only when a document was actually fetched without login; otherwise it is flagged unverified.
- **Limitations:** (a) many portals are JS-rendered, so a spec can exist behind a page that returns empty HTML to a fetcher — noted per provider; (b) several carriers gate the actual OpenAPI/WSDL behind a developer/commercial account, so "OpenAPI exists" sometimes means "exists but not anonymously downloadable"; (c) API surfaces change — current as of 2026-06-14; (d) this is desk research to support the SDD, **not** a procurement or contractual assessment.

## 3. Executive summary

1. **Rate quoting is not universal.** Of 30 direct carriers, only a subset expose a *callable rate/quote endpoint* in public docs. Many national posts and last-mile carriers (Evri, Yodel, DPD, GLS, PostNL, Correos, Colissimo) publish **label/tracking/shipment** APIs but **no public price-quote** — their pricing is contractual rate cards, not an endpoint. A rate-connector "factory" must scope to rate-capable providers; label/track-only carriers are out of MVP scope.
2. **Exactly one direct carrier offers an official, anonymously-downloadable OpenAPI spec for rating: UPS** (`Rating.yaml` in the official `UPS-API` GitHub org, MIT-licensed). FedEx, USPS and DHL Express all have modern REST rate APIs, but their canonical specs are JSON-collections / portal-gated / commercial-account-gated respectively.
3. **The cleanest deterministic OpenAPI generation targets are the aggregators**, not the carriers: **ShipEngine** (single OpenAPI 3.0 file + free `TEST_` sandbox), **Shippo** (OpenAPI 3.1 + official C#/.NET SDK), **EasyPost** (Swagger + best-in-class .NET SDK). These double as the **canonical-model reference** for TwinCore's `RateQuoteResult`/`QuoteOption`.
4. **The SOAP/WSDL path has excellent free test artifacts.** **Canpar** serves a *publicly reachable, login-free* `CanparRatingService?wsdl` with a `rateShipment` operation (verified by live fetch). **Canada Post** Get Rates ships WSDL/XSD + an **official C# sample** + free test service. **Aramex** has a dedicated Rates-Calculator WSDL + official C# samples. **Chronopost** publishes a `QuickcostServiceWS?wsdl` price-quote operation. Any of these proves the WSDL→C# branch without credentials.
5. **The legacy SOAP assumption for the big integrators is outdated.** FedEx and UPS have **deprecated** their old SOAP Web Services; USPS retired Web Tools (2026-01). So the generator's SOAP path should be anchored on **Canada Post / Canpar / Aramex / Chronopost**, *not* FedEx/UPS.
6. **Recommended first targets:** aggregator smoke target **ShipEngine** (fastest path to an end-to-end deterministic generation demo on a free sandbox); first real-carrier OpenAPI **UPS**; first real-carrier REST-without-OpenAPI **Australia Post** (free key, clean PAC rate API); first SOAP **Canpar** or **Canada Post**. This sequence exercises all three input families (OpenAPI / REST-docs / WSDL) on free, testable providers.

## 4. Classification legend

Documentation/spec availability (a provider may carry several):

| Code | Meaning |
|---|---|
| A | OpenAPI / Swagger confirmed |
| B | REST API docs confirmed, OpenAPI not confirmed (or behind login) |
| C | SOAP / WSDL / XSD confirmed |
| D | SDK / code samples / Postman collection confirmed |
| E | Documentation/examples only (e.g. PDF spec) |
| F | Developer-portal login / commercial account required |
| G | Not enough public evidence |
| H | Aggregator API with standardized multi-carrier rate model |

MVP suitability (request labels): `FIRST_MVP_CANDIDATE`, `GOOD_SECOND_CANDIDATE`, `SOAP_TEST_CANDIDATE`, `DOCS_ONLY_TEST_CANDIDATE`, `AGGREGATOR_REFERENCE_MODEL`, `DEFER`, `NEEDS_MANUAL_CHECK`.

Evidence confidence per row: `[O]` official-verified · `[3]` third-party · `[M]` model-knowledge-only.

## 5. Provider research matrix — first 40 providers

### Direct carriers (1–30)

| # | Provider | Official dev URL | Spec evidence | Rate endpoint? | Class | Diff | MVP status | Conf | Why (1 line) |
|---|---|---|---|---|---|---|---|---|---|
| 1 | FedEx | developer.fedex.com | REST + JSON-collection; OpenAPI not standard-public; legacy SOAP deprecated | Yes — Rates & Transit Times `POST /rate/v1/rates/quotes` | B,D,F | med | GOOD_SECOND_CANDIDATE | O | Clean REST + OAuth2 but no anonymously-downloadable OpenAPI; consume JSON collection or hand-model |
| 2 | UPS | developer.ups.com + github.com/UPS-API | **OpenAPI `Rating.yaml` (MIT, downloadable)** | Yes — Rating API | A,D | low-med | **FIRST_MVP_CANDIDATE** | O | Only carrier with an official downloadable rating OpenAPI; budget for known path/tag quirks (issue #132) |
| 3 | USPS | developer.usps.com | REST + OAuth2; OpenAPI behind portal; Web Tools retired 2026-01 | Yes — Domestic Prices 3.0 | B,D,F | med | GOOD_SECOND_CANDIDATE | O | Modern REST, free, but spec needs portal account; price/transit split across two APIs |
| 4 | DHL Express | developer.dhl.com | **OpenAPI exists** but portal + DHL-customer-account gated; BasicAuth | Yes — MyDHL Rating `/rates` | A,F,D | med | GOOD_SECOND_CANDIDATE | O | Real OpenAPI + clean rate, but spec & test both need a commercial DHL account |
| 5 | Canada Post | canadapost-postescanada.ca/.../developers | **SOAP WSDL/XSD + official C#/Java/PHP samples**; free test | Yes — Get Rates `getRates` | C,D | med-high | **SOAP_TEST_CANDIDATE** | O | Best SOAP exercise: free testable Get Rates + official C# sample |
| 6 | Purolator | eship.purolator.com | SOAP/ASMX WSDL; dev-key registration | Yes — Estimate | C,D,F | high | SOAP_TEST_CANDIDATE (2nd) / DEFER | O | Valid second SOAP but ASMX, versioned services, dev-key onboarding, thin first-party samples |
| 7 | Royal Mail | developer.royalmail.net | Portal-login-gated; REST + some SOAP underneath | Yes — Online Postage API (behind login) | F | med | NEEDS_MANUAL_CHECK | O/M | Real postage-price capability but full schema behind a Royal Mail dev account |
| 8 | Australia Post | developers.auspost.com.au | Public REST (no formal OpenAPI); free key | Yes — PAC `…/calculate` (domestic+intl) | B | low | **FIRST_MVP_CANDIDATE** | O | Cleanest free public REST rate API of all carriers; GET+JSON, self-serve key |
| 9 | New Zealand Post | nzpost.co.nz dev + MuleSoft Anypoint | REST; likely RAML/OAS on Anypoint; account-gated | Yes — Rate Finder / ShippingOptions | B (A-leaning) | low | GOOD_SECOND_CANDIDATE | O | Solid REST rate, two generations; verify Anypoint OAS for codegen |
| 10 | La Poste / Colissimo | colissimo.entreprise.laposte.fr | Legacy SOAP WSDL + newer REST SLS (OpenAPI/Redoc) | No public rate quote (label/track; pricing contractual) | C,B | high/med | SOAP_TEST_CANDIDATE | O | Dual-stack good for SOAP test, but no callable rate quote lowers RATE value |
| 11 | Chronopost | ws.chronopost.fr | **SOAP `QuickcostServiceWS?wsdl`** (price-quote) | Yes — QuickCost | C | high | **SOAP_TEST_CANDIDATE** | O | Public downloadable WSDL with an explicit price operation — best EU SOAP *rate* target |
| 12 | DPD | geoapi.dpd.cz + national units | REST (no public OpenAPI); fragmented per country | No (UK confirmed none; pricing contractual) | B | med-high | DEFER | O/M | No public rate quote + heavy per-country fragmentation |
| 13 | GLS | shipit.gls-group.eu | REST + SOAP twin documented | No rate operation (verified) | B,C | med | DEFER / SOAP_TEST (2nd) | O | Good public ShipIT docs but explicitly no rate op |
| 14 | Hermes / Evri | (no public portal) | Account-manager gated; only via aggregators | No — "EVRi does not have a Rates API" | F | unknown | DEFER | O(3) | No public docs, explicitly no Rates API (rates are static cards) |
| 15 | Yodel | (no public portal) | Partner/aggregator only | No — "does not send estimated rates" | F | unknown | DEFER | O(3) | Label/track only via partners; no rate quote |
| 16 | PostNL | developer.postnl.nl | REST + Postman + SOAP twins | No cost endpoint (DeliveryDate/Timeframe ≠ price) | B | low-med | GOOD_SECOND_CANDIDATE (connector) / DEFER (rate) | O | Best-documented public REST portal here, but no cost-quote endpoint |
| 17 | Correos | developers.correos.es (Anypoint) | REST + JWT modules; legacy SOAP PreRegistro | No rate module among ~23 assets | B,C | med | DEFER (rate) | O | Real modular REST + legacy SOAP, but no exposed rate module |
| 18 | Poste Italiane | apigardent.gp.posteitaliane.it | Official portal login-gated; OAS3 + pricing only via a reseller | Yes via reseller (direct unconfirmed) | F | unknown | NEEDS_MANUAL_CHECK | 3/M | Pricing OAS3 seen only on reseller (ufficiopostale/openapi.com); direct portal gated |
| 19 | Japan Post | post.japanpost.jp/bizpost | Postal-code REST API real; shipping/rate contract-gated | Unknown (only web simulator + aggregators) | F,G | unknown | NEEDS_MANUAL_CHECK | M | Genuine postal-code API, but no verified public Yu-pack rate API |
| 20 | Singapore Post | developer.singpost.com | Official portal; verified surface = tracking | Unknown (rate calculator on site, no confirmed dev endpoint) | F | med | NEEDS_MANUAL_CHECK | 3 | Real portal but rate endpoint not verified; log in to confirm |
| 21 | Hongkong Post | ec-ship.hongkongpost.hk/API-portal | **PDF spec (not OpenAPI)** + open-data rate JSON; account-gated | Yes — postage-charge calculation | E,F | med | **DOCS_ONLY_TEST_CANDIDATE** | O | Real EC-Ship API w/ postage calc + PDF spec; no machine spec → hand-model |
| 22 | China Post / EMS | (no official EN portal) | Only third-party aggregators | Unknown (consumer calculator only) | G | unknown | DEFER / NEEDS_MANUAL_CHECK | M | No official public API located; reach only via aggregators |
| 23 | Aramex | aramex.com/developers | **SOAP Rates-Calculator WSDL + official C# samples**; REST emerging | Yes — Rate Calculator `CalculateRate` | C | med | **SOAP_TEST_CANDIDATE** | O | Canonical SOAP+WSDL with dedicated rates WSDL + official C# sample |
| 24 | SF Express | open.sf-express.com (Fengqiao) | Portal login-gated; signature auth; CN-centric | Unknown (rate cards often offline) | F | high | DEFER / NEEDS_MANUAL_CHECK | 3 | Sign-up-gated, signature auth, public rate endpoint unconfirmed |
| 25 | Yamato Transport | B2 Cloud API (Business Members) | Login-gated; waybill-oriented | Unknown/No (label-focused) | F | unknown | NEEDS_MANUAL_CHECK | M | Official B2 Cloud API (2024) but no verified public rate endpoint |
| 26 | Sagawa Express | e-Hiden (contract-gated) | No public portal | Unknown | G | unknown | DEFER / NEEDS_MANUAL_CHECK | M | No official public API; e飛伝 contract-gated; rate only via aggregators |
| 27 | OnTrac | west.ontrac.com + V3 (account) | Legacy XML + REST V3; account-gated; no public spec | Yes (gated) — rate & transit quote | F | high | NEEDS_MANUAL_CHECK | O | Rating exists but no public OpenAPI/WSDL; contract emailed by carrier |
| 28 | LaserShip | (merged into OnTrac) | Folded into OnTrac V3 (EasyPost 2024) | Unknown (folded) | G | unknown | DEFER | O | Absorbed into OnTrac; no separate dev docs — treat as OnTrac |
| 29 | Canpar Express | sandbox.canpar.com/canshipws | **`CanparRatingService?wsdl` — publicly reachable, login-free (live-fetched)** | Yes — `rateShipment` | C | med | **SOAP_TEST_CANDIDATE (best)** | O | Clean public login-free WSDL with rating op — ideal SOAP-path artifact |
| 30 | Loomis Express | canshipws (shared w/ Canpar, TFI) | Same SOAP stack; own-domain WSDL not separately reachable | Yes — `rateShipment` (same as Canpar) | C | med | SOAP_TEST_CANDIDATE | O/3 | Identical codegen to Canpar; only live endpoint host + creds differ |

### Aggregators (31–40) — standardized multi-carrier rate model (class H)

| # | Provider | Official dev URL | Spec evidence | Rate endpoint? | Class | Diff | MVP status | Conf | Why (1 line) |
|---|---|---|---|---|---|---|---|---|---|
| 31 | Shippo | docs.goshippo.com | **OpenAPI 3.1 downloadable + official C#/.NET SDK**; free test key | Yes — Shipment → `rates[]` | H,A,B | low | **AGGREGATOR_REFERENCE_MODEL** (+FIRST_MVP) | O | Richest normalized rate object (provider/servicelevel/dual-currency/estimated_days/attributes) — best canonical reference |
| 32 | EasyPost | docs.easypost.com | **Swagger + best-in-class C#/.NET SDK + Postman**; free test key | Yes — Shipment `rates[]` + SmartRate | H,A,B | low | **AGGREGATOR_REFERENCE_MODEL** (+FIRST_MVP) | O | Triple rate model (rate/list_rate/retail_rate) = reference for negotiated vs published vs retail |
| 33 | ShipEngine | shipengine.com/docs | **Single OpenAPI 3.0 on GitHub + free `TEST_` sandbox** | Yes — `POST /v1/rates` | H,A,B | low | **FIRST_MVP_CANDIDATE** (+AGG_REF) | O | Cleanest single spec + zero-cost sandbox → best early deterministic-generation smoke target |
| 34 | ShipStation | docs.shipstation.com | **V2 = ShipEngine rebrand (same OpenAPI)**; V1 legacy separate | Yes — `/v2/rates` | H,A,B | low | DEFER (redundant) | O | Same spec as ShipEngine — generate once; only touch V1 if a client needs the old order API |
| 35 | AfterShip | aftership.com/docs/shipping | REST Rates API; OpenAPI not clearly downloadable; tracking-first | Yes — `POST /rates` | H,A,D | med | GOOD_SECOND_CANDIDATE | O/3 | Good `total_charge{amount,currency}` + transit model; weaker .NET SDK & spec story |
| 36 | Sendcloud | sendcloud.dev | OpenAPI-backed v2/v3 Redoc; no first-class .NET; test mode | Yes — v3 shipping options (quotes) | H,A,B | med | GOOD_SECOND_CANDIDATE | O | Strong EU coverage + real OpenAPI, but v2/v3 split + payment gating add friction |
| 37 | ShippyPro | shippypro.com/…API-Documentation | No public OpenAPI; Enterprise-plan-gated | Yes — `GetRates` | H,F | high | DEFER | O/M | Multi-carrier GetRates exists but Enterprise-only, no OpenAPI/.NET — can't self-serve |
| 38 | Metapack | dev.metapack.com (Manhattan) | Partial Redoc; enterprise sales-gated | Yes (conceptual — optimal carrier service) | H,F | high | DEFER | O/M | 300+ carriers but account-gated, only partial public spec |
| 39 | nShift | nshift.com/apis + Transsmart devdocs | Fragmented products, mixed auth, no unified OpenAPI | Yes — rate selection via Shipping Rules | H,F | high | DEFER | O/M | Disjoint product APIs + mixed auth; costly codegen, enterprise-onboarded |
| 40 | Easyship | developers.easyship.com | **OpenAPI + `llms.txt` + free no-CC sandbox** | Yes — Shipping Rates API | H,A,B | low-med | GOOD_SECOND_CANDIDATE (+AGG_REF ranking) | O | Free sandbox + OpenAPI + ranking flags (cheapest/fastest/value) — agent-friendly second target |

**Tally:** rate-capable + low-friction public docs: ~9 (UPS, AusPost, NZ Post + aggregators Shippo/EasyPost/ShipEngine/Easyship/AfterShip/Sendcloud). Rate-capable via SOAP/WSDL: 5 (Canpar, Loomis, Canada Post, Aramex, Chronopost; + Purolator/Colissimo SOAP without/with rate). Rate-capable but gated/docs-only: FedEx, USPS, DHL, Royal Mail, HK Post. No public rate quote / defer: DPD, GLS, Evri, Yodel, PostNL, Correos. Needs manual portal check: Royal Mail, Poste Italiane, Japan Post, SingPost, SF Express, Yamato, Sagawa, OnTrac, China Post.

## 6. Best first MVP candidates

Ranked, with rationale tied to the SDD's deterministic-first goal:

1. **ShipEngine (aggregator) — first end-to-end smoke target.** Single OpenAPI 3.0 file + free `TEST_` sandbox means the generator's *deterministic OpenAPI → C# client → adapter → mapper → fixture test* chain can be proven with **zero credentials cost and zero LLM**. Its rate response is also a clean canonical reference.
2. **UPS (carrier) — first real-carrier OpenAPI.** The only direct carrier with an official downloadable rating OpenAPI (`Rating.yaml`, MIT). Proves the generator on a genuine carrier spec, not just an aggregator. Budget a little time for documented spec/path quirks.
3. **Australia Post (carrier) — first REST-without-OpenAPI.** Free self-serve key, clean PAC `…/calculate` rate endpoint, GET+JSON. Proves the "REST docs, no OpenAPI → hand-modelled provider schema" path on a free, testable carrier.
4. **Shippo / EasyPost (aggregators) — reference + .NET-native seconds.** Both have official C#/.NET SDKs and free test keys; ideal to validate generated-adapter ergonomics against a real SDK.

This set covers all three SDD input families (OpenAPI, REST-docs, and — see §7 — WSDL) on **free, anonymously testable** providers, which is exactly what a first generator gate needs.

## 7. Best SOAP/WSDL test candidates

1. **Canpar Express — best.** `https://sandbox.canpar.com/canshipws/services/CanparRatingService?wsdl` was **fetched live, no login**, valid WSDL, `rateShipment` operation, DTO XSDs at `http://dto.canshipws.canpar.com/xsd`. This is a ready downloadable artifact to exercise the WSDL→C# (`dotnet-svcutil`) branch end-to-end. (Loomis Express shares the same `canshipws` stack — same codegen, creds differ.)
2. **Canada Post Get Rates — best for review quality.** Downloadable WSDL/XSD + soapUI project + an **official C# sample** + a free test service. The official C# sample is gold for validating generated output against a known-good reference.
3. **Aramex Rates Calculator — best global SOAP rate.** Dedicated `aramex-rates-calculator` WSDL with `CalculateRate` + official C# samples; `ClientInfo` credential block is the main manual plumbing.
4. **Chronopost QuickCost — best EU SOAP rate.** Public `QuickcostServiceWS?wsdl` with an explicit price operation.
5. Secondary: **Purolator** (ASMX, dev-key gated), **Colissimo/GLS SOAP** (no rate op — structural test only).

Note: do **not** anchor the SOAP path on FedEx/UPS — their SOAP Web Services are deprecated.

## 8. Best OpenAPI / Swagger candidates

- **Carrier:** **UPS** (`Rating.yaml`, official, MIT, downloadable) — the single best real-carrier OpenAPI. DHL Express *has* an OpenAPI but it is commercial-account-gated; USPS/FedEx specs are portal-gated / JSON-collection respectively.
- **Aggregators (cleanest specs):** **ShipEngine** (OpenAPI 3.0, single file, GitHub), **Shippo** (OpenAPI 3.1, ~932 KB, GitHub mirror), **Easyship** (OpenAPI + `llms.txt`), **EasyPost** (Swagger UI + Postman). All four are downloadable and back a free test tier.
- **Practical implication:** the deterministic OpenAPI importer (already shipped in G1) should be validated first against **ShipEngine + UPS**; both are real specs with the nested/array shapes the importer must handle (UPS rated-shipment arrays mirror the G1 `MockCarrierA` `rateReplyDetails[*]` design).

## 9. Best docs-only / LLM-assisted candidates

These are where deterministic parsing is insufficient and design-time LLM assistance earns its place (never runtime):

- **Hongkong Post EC-Ship — primary docs-only candidate.** Real postage-calculation API but the contract is a **PDF spec** + open-data rate JSON, not a machine-readable spec → an LLM can draft the endpoint/field model from the PDF, then a human approves before deterministic generation.
- **Royal Mail / Poste Italiane / SingPost / OnTrac** — REST capability exists but the schema sits behind a portal; once a developer pastes the post-login docs, an LLM-assisted pass can propose the schema for review.
- **FedEx** — its public artifact is a JSON request/response *collection* rather than a full OpenAPI; an LLM can help infer a complete schema/enum set from the samples, human-reviewed.
- **General rule from the data:** docs-only/LLM-assisted is the *minority* path. The majority of rate-capable providers have either OpenAPI (UPS, aggregators) or WSDL (Canpar, Canada Post, Aramex, Chronopost) — both deterministically parseable. LLM assistance is for the long tail (PDF specs, portal-only docs), not the core.

## 10. Best aggregator reference models

Aggregators already solved "normalize many carriers into one rate object" — their response shapes are the best external validation of TwinCore's canonical `RateQuoteResult`/`QuoteOption`:

| Aggregator | Rate object highlights | What TwinCore should borrow |
|---|---|---|
| **Shippo** | `provider`, `servicelevel{name,token,display_name}`, `amount`+`currency` **and** `amount_local`+`currency_local`, `estimated_days`, `duration_terms`, `attributes[CHEAPEST\|FASTEST\|BESTVALUE]` | dual-currency (sender/recipient); a **ranking-attribute enum** for quote sorting |
| **EasyPost** | `carrier`, `service`, `rate`+`currency`, `list_rate`, `retail_rate`, `delivery_days`, `delivery_date_guaranteed` | **negotiated vs published vs retail** price distinction; guaranteed-delivery flag |
| **ShipEngine** | `carrier_id`, `service_code`, `shipping_amount{amount,currency}` + separate `insurance_amount`/`other_amount`, `delivery_days`, `estimated_delivery_date`, `trackable` | **cost breakdown** as separate typed amounts (vs one total) |
| **Easyship** | `courier_name`, `total_charge`+`currency`, `min/max_delivery_time`, `value_for_money/cheapest/fastest` flags | **transit as a min/max range**; ranking flags |

**Recommendation:** TwinCore's current `QuoteOption` (CarrierCode, ProviderServiceCode, ServiceName, Price{Amount,Currency}, EstimatedTransitDays, EstimatedDeliveryDate, IsAvailable, Warnings) is well-aligned with the common subset. Consider, as *optional* canonical fields for later gates: (a) a price-type/`list`/`retail` distinction (EasyPost), (b) a ranking attribute (Shippo/Easyship), (c) a transit min/max range, (d) a typed surcharge/charge breakdown (ShipEngine). These are additive and should not block G2.

## 11. Providers requiring manual developer-portal check

Log in / request access, then re-run a focused pass: **Royal Mail** (Online Postage schema), **Poste Italiane** (direct portal vs reseller OAS), **Japan Post** (Yu-pack rate API existence), **Singapore Post** (rate endpoint in portal), **SF Express** (Fengqiao rate interface), **Yamato** (B2 Cloud rate vs label-only), **Sagawa** (any e飛伝 API), **OnTrac** (V3 contract), **China Post/EMS** (any official API). For each, the open question is narrow: *is there a callable rate/quote endpoint, and what is its request/response schema?*

## 12. Recommendation for the small initial tool (ANSWER REQUIRED)

The first small tool should **include** (refines the request's hypothesis with the findings):

1. **Input import:** OpenAPI/Swagger (JSON/YAML) — *primary, deterministic*; WSDL — *deterministic via `dotnet-svcutil`*; docs/examples (incl. PDF) — *LLM-assisted, human-approved*. (G1 already ships the OpenAPI importer.)
2. **Provider classification** by input family (A/B/C/E above) + **rate-endpoint detection** (operations named Rate/Rates/Quote/CalculateRate/getRates/QuickCost) — flag carriers with **no rate endpoint** as out-of-scope early (the §3 finding makes this essential).
3. **Provider schema browser** (G1) + **internal canonical model browser** (G1).
4. **Basic mapping profile** (provider field → canonical `QuoteOption`), with explicit **IterationRoot** for the rate-array and no implicit `[0]` (G2).
5. **Deterministic C# generation** from an approved profile: API client (HttpClient-based for REST, svcutil-wrapped for SOAP), `IRateProvider` adapter, request mapper, response mapper, error mapper, options class, DI extension.
6. **Production-oriented scaffolding:** typed options bound from appsettings, retry/timeout policy hooks (Polly or `Microsoft.Extensions.Http.Resilience`), cancellation tokens, structured logging without secrets, typed error mapping.
7. **Fixture-based tests** (request/response/error mappers + options binding + adapter-from-fixture), runnable **without real credentials**.
8. **Generation manifest + MANUAL_REVIEW_CHECKLIST** (input hash, profile hash, file list, warnings, unresolved decisions).
9. **First fixtures = free testable providers:** ShipEngine (OpenAPI), UPS (OpenAPI), Australia Post (REST), Canpar/Canada Post (WSDL). Plus the existing synthetic `MockCarrier*` set for hostile/edge coverage.

## 13. What the small initial tool should NOT include (ANSWER REQUIRED)

- **No real carrier credentials**, no paid signup, no live/sandbox carrier calls inside the generator gate (separate external-access gate only).
- **No runtime LLM** — zero LLM on the Rate API request/price/mapping path. LLM is design-time only.
- **No shipment creation, tracking, labels, payments, pickup scheduling** — rate quoting only.
- **No automatic PR / main merge / source-tree insertion** — generated output is a reviewable package; a human integrates it.
- **No production-readiness claim** without developer review + sandbox/real evidence.
- **No `TwinCore.sln` / `Src/**` / Azure work-item / pipeline edits** — isolated under `AiIntegrator/**`.
- **No full docs-only automation** — LLM suggestions from docs must be human-approved before deterministic generation.
- **No provider-specific hacks in the factory core** — a new provider must not require core changes (factory-proof rule).
- **No secrets in generated source** — auth fields are placeholders bound from secret store/appsettings.

## 14. LLM minimization strategy (ANSWER REQUIRED)

The market data confirms LLM can be kept to a **minority, design-time-only** role, because most rate-capable providers expose machine-readable contracts:

**Deterministic (no LLM):**
- OpenAPI/Swagger parsing → provider schema (UPS, aggregators) — the common case.
- WSDL/XSD parsing → SOAP client + schema (Canpar, Canada Post, Aramex, Chronopost) via `dotnet-svcutil`.
- DTO/client generation; adapter/mapper/options/DI/test emission from an **approved mapping profile** (Roslyn `SyntaxFactory` preferred).
- Manifest/hash generation; fixture-based test generation; re-generation from a stored profile (must be reproducible).

**LLM-assisted (design-time only, human-approved, never on the runtime path):**
- Interpreting **docs-only / PDF** specs (HK Post) and portal-only docs (Royal Mail, OnTrac).
- Endpoint/rate-operation discovery from unstructured docs.
- Draft field-mapping suggestions (provider path → canonical) — always `NeedsReview` until a human approves.
- Auth-model and error-model explanation; test-scenario suggestions.

**Forbidden (hard rule):**
- LLM during a live Rate API request, price calculation, or response mapping at runtime.
- LLM as a production dependency of the generated adapter.

**Quantified expectation from this research:** of the ~14 rate-capable providers worth pursuing, ~9 are fully deterministic (OpenAPI or WSDL) and only ~5 (FedEx JSON-collection, HK Post PDF, Royal Mail/Poste Italiane/OnTrac portal docs) would benefit from design-time LLM assistance — so the deterministic path covers the clear majority.

## 15. Suggested updates to the SDD spec

Tie-backs to `00_MASTER_SDD_SPEC.md` and siblings (advisory — AIKB edits belong to GPT after audit):

1. **Re-anchor the SOAP examples.** The integration spec lists Canada Post/Purolator for SOAP — correct; **add Canpar** (publicly reachable login-free WSDL — the easiest SOAP test artifact) and **Aramex/Chronopost** (rate-bearing WSDLs). Explicitly note FedEx/UPS SOAP is **deprecated** so nobody anchors SOAP work on them.
2. **Name the first real fixtures.** §16 assumptions say "first safe input is a mock OpenAPI fixture" — keep that, and add a concrete real-spec ladder: **ShipEngine → UPS → Australia Post → Canpar/Canada Post**, all free/testable.
3. **Record the "not every carrier has a rate API" truth** (new risk/assumption): many posts/last-mile carriers are label/track-only; the factory must detect rate-endpoint presence and scope label-only carriers out of MVP. This materially affects provider selection and should be explicit in §15/§18.
4. **Validate the canonical model against aggregators.** The G1 `QuoteOption` matches the Shippo/EasyPost/ShipEngine common subset; consider optional additive fields (price-type, ranking attribute, transit min/max, charge breakdown — see §10) for a later gate. Confirms the G1 design choice rather than changing it.
5. **Answer NEEDS-CLARIFICATION #6** (first acceptance fixture): recommend **ShipEngine OpenAPI** as the first real generation acceptance fixture (free sandbox, clean spec), with the synthetic `MockCarrierA` retained for hostile/edge tests.
6. **Auth-model coverage** is validated by the data: OAuth2 client-credentials (UPS/USPS/FedEx), BasicAuth (DHL, GLS, EasyPost), API-key header (AusPost, ShipEngine, AfterShip), token (Shippo, Easyship), SOAP body credentials + `ClientInfo` (Canpar/Aramex/Canada Post). The spec's auth list already covers these — no gap.

## 16. Risks and unknowns

| Risk / unknown | Note |
|---|---|
| Portal-gated specs | UPS aside, the big-carrier OpenAPI/WSDL artifacts often need an account; "OpenAPI exists" ≠ "anonymously downloadable". Verified per provider. |
| JS-rendered portals | Some docs returned empty HTML to a fetcher; rate-endpoint existence confirmed via search where the page was un-fetchable — flagged `[3]`/`[M]`. |
| Spec quality | UPS OpenAPI has documented path/tag inconsistencies (issue #132) — the importer must tolerate imperfect specs (G1 already degrades gracefully). |
| No-rate-API carriers | Evri/Yodel/DPD/GLS/PostNL/Correos confirmed label/track-only — wasted effort if the factory targets them for rate. |
| Manual-check tail | 9 providers (§11) could not be fully verified without login — their rate-API status is genuinely unknown, not negative. |
| Aggregator ToS | Using an aggregator's free test key for *generation testing* is fine; redistributing their OpenAPI or calling at scale has ToS limits — out of scope here, flag before any real use. |
| Currency/units | Carriers differ (KG/LB, CM/IN, multi-currency) — the mapping layer's UnitConvert/EnumMap (tech-design) is essential; aggregators show dual-currency is real (Shippo). |

## 17. Next safe step

1. **GPT audits this report** (trigger «отчёт»); on acceptance, GPT folds the findings into the SDD (§15 suggestions) and answers the open NEEDS-CLARIFICATION items — **AIKB writes are GPT's**, not this gate's.
2. **Recommended provider ladder for the generator gates:** G2 mapping/validation proven on **ShipEngine** (free OpenAPI sandbox) + the synthetic `MockCarrierA`; G3 generation/quote-demo on the same; G4 factory-proof adds **UPS** (real carrier OpenAPI) and a **SOAP** target (**Canpar** or **Canada Post**) to prove WSDL + multi-provider.
3. **No implementation in this gate.** This is research/spec support only. The next *execution* gate remains G2 (Mapping Workbench), which must be opened explicitly with its own ACTIVE_REQUEST after the blueprint v0.2 + SDD updates land.
4. **Owner-proxy note:** all recommendations are a provisional owner-approved baseline by Slava; reversible when Igor returns. No approval is attributed to Igor.

---

*Report written to `TwinCore/rate-api-carrier-docs-research-v0.1/report.md` and mirrored to `TwinCore/latest-report.md` + `TwinCore/_BRIDGE/LATEST_REPORT.md`. `STATUS.json` updated (state REPORT_READY, nextActor GPT) preserving the two prior pending audits (G1 import-browse, blueprint review). No source code, no AIKB writes, no branches, no live carrier calls. STOP — GPT will audit; no self-acceptance.*
