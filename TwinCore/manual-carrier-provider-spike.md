# Manual Carrier Provider Spike — UPS Rating via NSwag

PROJECT: TwinCore Universal API Connector Generator
GATE: Phase 0.2 — Manual Carrier Provider Spike / NSwag run
MODE: local generation + inspection only (no generator, no CLI/WPF, no TwinCore runtime, no credentials, no live/paid UPS calls)
DATE: 2026-06-16
AUTHOR: Claude (researcher). All claims below are from the locally generated+built output; file:line cited.

> Universal API Connector Generator, not a Rate API generator — Rate API is the first proof case. This gate validates the future generator algorithm by running the **first real carrier spec** through NSwag and inspecting what a human/generator must still resolve by hand.

---

## 1. Did NSwag run? (headline)

- **First attempt on the YAML: FAILED** — not an NSwag fault, a YAML-parser rejection in NSwag's NJsonSchema reader:
  `(Line: 8, Col: 1) While scanning a literal block scalar, found extra spaces in first line.`
  Cause: `Rating.yaml` line 6 is `  description: |` (literal block), line 7 is `  ` — a **whitespace-only line indented to the key level (2 spaces), not the content level (4)**. YamlDotNet (NJsonSchema's reader) rejects it; spec-compliant parsers accept it.
- **One reasonable adjustment (per step 5): convert YAML → JSON** with a compliant parser (PyYAML), preserving semantics 100% (`openapi 3.0.3`, 2 paths, 150 schemas), then feed NSwag the JSON.
- **Second attempt on the JSON: SUCCEEDED** — exit 0, `Code has been successfully written to file.`
- **Generated client BUILDS: 0 Warning / 0 Error, exit 0** (net9 classlib + Newtonsoft.Json 13.0.3).

Generation is **stable**. No spec content was edited; the only change was input format (YAML→JSON).

---

## 2. Artifacts & locations (all under `TwinCore/manual-carrier-provider-spike/`)

| Artifact | Path |
|---|---|
| Downloaded spec (original) | `input/Rating.yaml` (240 KB, 5937 lines) |
| Converted spec (what NSwag consumed) | `input/Rating.json` (openapi 3.0.3, 2 paths, 150 schemas) |
| Generated client | `generated/UpsRatingClient.cs` (370 KB, 5271 lines, 147 types) |
| Build project (proof of compile) | `generated/UpsRatingTrial.csproj` |
| Provider skeleton notes | `notes/provider-skeleton.md` |

Source spec: `https://raw.githubusercontent.com/UPS-API/api-documentation/main/Rating.yaml`. `bin/obj` git-ignored.

---

## 3. Generation & build evidence

| | Value |
|---|---|
| Tools | dotnet 9.0.304, NSwag.ConsoleCore 14.7.1 |
| Options used | `/GenerateClientInterfaces:true /GenerateOptionalParameters:true /GenerateNullableReferenceTypes:true` (exactly as specified; no `/jsonLibrary`) |
| Output | 1 file, **5271 lines**, 370 839 bytes, **147 types** (`IClient` + `Client` + ~145 DTOs/enums) |
| Build | net9 + Newtonsoft.Json 13.0.3 → **0W / 0E, exit 0** |

---

## 4. Inspection findings (step 6 checklist)

- **Readable / reviewable?** Yes as generated code, with a caveat of *scale*: one 5271-line file is auditable but large; the client logic is conventional `HttpClient.SendAsync` (`UpsRatingClient.cs:217`) you can read top-to-bottom. Header carries the usual NSwag pragma-suppression block (`:9–23`).
- **HttpClient usage:** native typed client — ctor `Client(System.Net.Http.HttpClient httpClient)` (`:106`) → registers as `AddHttpClient<IClient, Client>()`. `SendAsync` with `ResponseHeadersRead` + `ConfigureAwait(false)` + cancellation (`:217`).
- **Auth hooks:** `partial void PrepareRequest(...)` ×2 + `ProcessResponse(...)` (`:138–140`). UPS uses **OAuth2 bearer**, which is **not** in the generated code — inject via a `DelegatingHandler` on the `HttpClient` or the `PrepareRequest` partial. `transId`/`transactionSrc` are added as request headers in-method (`:187`).
- **Nullable quality:** `/GenerateNullableReferenceTypes:true` works — `#nullable enable` (`:7`), optional params are `string?` (`:57`), `ApiException.Response` is `string?` (`:6344`). The **public surface is properly annotated** — a clear improvement over the Phase-0.1 default run. (Internal codegen warnings are still pragma-suppressed at the top; required strings use `= default!`.)
- **DTO shape:** deep UPS envelopes — `RATERequestWrapper` / `RATEResponseWrapper` wrapping `RateRequest.Request.Shipment...`. 147 types for a single rate operation; mapping flattens a lot.
- **Error response shape:** typed and granular — distinct branches for **400 / 401 / 403 / 429** plus a fallback, each throwing `ApiException<ErrorResponse>` with a semantic message ("Invalid Request", "Unauthorized Request", "Blocked Merchant", "Rate Limit Exceeded") (`:243–285`). `ApiException<TResult>` carries the deserialized body (`:6363`).
- **JSON library:** **Newtonsoft.Json** by default (no `/jsonLibrary` was passed) — `[Newtonsoft.Json.JsonProperty]`, `Newtonsoft.Json.JsonConvert.SerializeObject` (`:102,:189`). For our owned/dependency-light goal, regenerate with `/jsonLibrary:SystemTextJson` (Phase 0.1 confirmed STJ output needs **zero** runtime deps).

---

## 5. Mapping ambiguities — the real generator signal

Running a **real** spec (vs the 0.1 mock) surfaced what a generator cannot infer and must flag in an unresolved-mapping log:

1. **Money is `string`, not a number.** UPS: `public string MonetaryValue` with `Required.Always` (`:2613` and ~20 more sites). The mock used `format:decimal`. → our response mapper must parse `string → decimal` (`InvariantCulture`) and **fail loudly**, never default to 0.
2. **Currency is a separate, conditionally-required sibling field** ("Required if a value … exists in the MonetaryValue tag"). → canonical `MoneyValue(amount, currency)` must **join** two fields the spec keeps apart.
3. **Multiple charge buckets** (TotalCharges / itemized / negotiated) — which is `QuoteOption.Price`? A business decision the generator must surface, not guess.
4. **`requestoption` (Rate/Shop/…) is not 1:1 with a canonical service code** — needs a decision table.
5. **Units** (weight/dimension) are UPS code-strings, not enums → explicit unit mapping.

These five are the concrete content of the "unresolved-mapping log + review checklist" the generator must emit.

---

## 6. Provider skeleton (notes only — not implemented)

Per step 7 (generation stable), skeleton **notes** for the six owned types are in `notes/provider-skeleton.md`: `IRateProvider` (canonical, from G1), `UpsRateProvider`, `UpsRateRequestMapper`, `UpsRateResponseMapper`, `UpsErrorMapper`, `UpsRateOptions`. No code was written for them — the file records responsibilities + the mapping decisions above. The generated client is the bottom layer; these six are the layer we own on top.

---

## 7. Risks / blockers

- **Blocker encountered & resolved:** YAML literal-block parse failure → YAML→JSON conversion. Implication for the generator: **it must run a YAML normalization/parse stage** (or accept JSON), because real carrier YAML is not guaranteed to satisfy NJsonSchema's reader.
- **No hard blocker remaining.** Generation + build are stable.
- **Watch items:** Newtonsoft default (switch to STJ); 5271-line single file (consider `/GenerateContractsForResponseTypes` or per-class output for review ergonomics); OAuth not generated (handler needed); deep envelopes make the mapper the real cost, not the client.

---

## 8. Recommendation / next safe step

NSwag is a **viable client-layer baseline for a real carrier** — it ingested a 150-schema UPS spec (after a trivial format conversion) and produced a building, typed, DI-friendly, NRT-annotated client. Confirms the Phase 0.1 "A — NSwag first" recommendation against real-world input.

**Next safe step (verbatim in STATUS.json):** *"Review UPS manual provider spike and draft generator algorithm."* The generator-algorithm draft should encode: input normalization (YAML→JSON), `/jsonLibrary:SystemTextJson`, and an unresolved-mapping log seeded by the five ambiguities in §5. Drafting the algorithm is a separate bounded step — not opened here.

---

## MINIMAL_IMPLEMENTATION_GATE
- plugin active: yes — `ponytail@ponytail` v4.4.0 (SessionStart hook reports full mode).
- mode used: full.
- larger solution avoided: did not build the generator, a CLI, an abstraction, or any of the six provider types (notes only); did not edit the UPS spec content; did not chase multiple NSwag flag permutations (one adjustment, per step 5).
- minimal solution chosen: download → one YAML→JSON conversion → NSwag with the given options → build to prove compile → read + characterize → notes. Reused the Phase-0.1 NSwag findings instead of re-deriving them.
- why enough for this spec: the gate asks whether NSwag runs on a real carrier, whether output builds, and what a human must still resolve — all answered with file:line + build evidence.
- deferred upgrade path: regenerate with STJ; add a YAML-normalization stage to the future generator; implement the six provider types only when the generator-algorithm gate is opened.
