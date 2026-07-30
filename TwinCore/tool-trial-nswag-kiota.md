# Tool Trial — NSwag vs Kiota (low-level C# client baseline)

PROJECT: TwinCore Universal API Connector Generator
GATE: Phase 0.1 — Tool Trial Spike: NSwag vs Kiota
MODE: local tool evaluation only (no generator, no CLI/WPF, no TwinCore runtime, no credentials, no live calls)
DATE: 2026-06-16
AUTHOR: Claude (researcher). All claims below are from generated output built locally; file:line cited.

> This is a **Universal API Connector Generator**, not a Rate API generator. Rate API is only the first proof case. This spike evaluates only the **low-level generated API client layer** — the bottom box of the future output (generated client → provider/adapter → request/response/error mappers → options/manifest/unresolved-mapping log). Mapping to our internal model is *our* layer and is out of scope here.

---

## 1. Recommendation (up front)

**A — Use NSwag as the first baseline**, with **C (support Kiota behind our client abstraction) as the documented deferred path.**

Why NSwag first, for the proof case:
- **Zero runtime dependency.** NSwag + System.Text.Json output builds on net9 with **no NuGet package at all** (verified 0W/0E). Kiota requires the `Microsoft.Kiota.*` runtime packages in the request path. Our stated goal is an *owned, reviewable, dependency-light* package — NSwag matches it most directly.
- **One reviewable file.** 513 lines, the real `HttpClient.SendAsync` visible and auditable in a single pass. Kiota's actual HTTP send lives inside the Kiota runtime lib, so the generated code is smaller but the behaviour is in a dependency you don't see.
- **Plain POCO DTOs** → trivial to wrap with our provider/adapter and hand-map into internal contracts. Kiota models carry `IParsable` serialization machinery (still mappable, just heavier).
- **Honors `format:decimal`** as `decimal` (money-safe) and **round-trips unmapped fields** via `[JsonExtensionData]` — directly useful for unresolved-mapping detection.

Why keep Kiota behind an abstraction (deferred, not now):
- First-class pluggable **auth providers** and a **built-in retry/redirect middleware** chain become valuable once we onboard many providers with diverse auth.
- **No-reflection `IParsable`** serialization is **AOT/trim-friendly** — matters if the connector layer ever ships in a trimmed/AOT host.

Not B (Kiota-first): its runtime-dependency + everything-nullable models + behaviour-in-library fight the "owned & reviewable" priority for the first proof. Not D: both are credible; rejecting both would be throwing away a working client layer we'd otherwise re-implement.

---

## 2. What was installed & tested (evidence)

| | Value |
|---|---|
| dotnet SDK | 9.0.304 |
| NSwag | NSwag.ConsoleCore **14.7.1** (`dotnet tool install --global NSwag.ConsoleCore`) |
| Kiota | Microsoft.OpenApi.Kiota **1.32.2** (`dotnet tool install --global Microsoft.OpenApi.Kiota`) |
| Input spec | `tool-trials/nswag-kiota/input/openapi.json` — **MOCK / synthetic** carrier Rate API (POST /rates), not a real carrier |
| NSwag generate | exit 0 → 1 file, 513 lines |
| Kiota generate | exit 0 → 13 files, 923 lines |
| NSwag build | net9 classlib, **no extra NuGet** → **0W/0E, exit 0** |
| Kiota build | net9 classlib + `Microsoft.Kiota.Bundle 2.0.0` → **0W/0E, exit 0** |

Generated output committed under `TwinCore/tool-trials/nswag-kiota/` (`nswag-output/`, `kiota-output/`, `input/`, `notes.md`); `bin/obj` git-ignored.

---

## 3. Side-by-side comparison

| Dimension | NSwag 14.7.1 | Kiota 1.32.2 |
|---|---|---|
| **Install friction** | One `dotnet tool` install, no setup. | One `dotnet tool` install, no setup. |
| **Command simplicity** | `nswag openapi2csclient /input /output /classname /namespace …` — many flags but one call. | `kiota generate -l CSharp -d -o -n -c` — short, fewer knobs. |
| **File structure** | **1 file**, everything inline (client+DTOs+enums+exception). | **13 files** — root client, `Models/*` (one per type + enums), `Rates/RatesRequestBuilder.cs`, `kiota-lock.json`. |
| **Client readability** | Conventional `HttpClient.SendAsync` you can read top-to-bottom (`:141`); pragma-heavy header. | Fluent `client.Rates.PostAsync` (`RatesRequestBuilder.cs:46`); real send hidden in runtime lib. |
| **Models readability** | Plain POCOs, `[JsonPropertyName]` + `[Required]`/`[StringLength]` annotations. | POCOs **plus** `IParsable` `GetFieldDeserializers`/`Serialize` per model (`Rate.cs:74–100`). |
| **HttpClient support** | Native — ctor takes `HttpClient` (`:59`). | Indirect — `IRequestAdapter` wraps an `HttpClient` (`:31`); default adapter = `HttpClientRequestAdapter`. |
| **DI friendliness** | Idiomatic `AddHttpClient<IMockCarrierClient,MockCarrierClient>()`. | Needs adapter + auth provider wiring; Kiota DI helpers exist but more parts. |
| **Auth injection** | Via `PrepareRequest` partial (`:91`) or an `HttpClient` `DelegatingHandler`. | **First-class** `IAuthenticationProvider` on the adapter (Anonymous/ApiKey/Bearer built in). |
| **Retry / timeout** | **Not built in** — add Polly on the `HttpClient` (`AddPolicyHandler`). | **Built-in** retry/redirect middleware in the default handler chain. |
| **Error handling** | Throws `ApiException` / `ApiException<ErrorResponse>` with status+body+headers+typed `.Result` (`:174,:580–613`). | `errorMapping` → `ErrorResponse : ApiException` thrown as a **typed catchable** exception (`ErrorResponse.cs:13`). |
| **Nullable / NRT** | Weak default: no `?` on ref props, blanket nullable-warning suppression (`:16–21`). Opt-in `/GenerateNullableReferenceTypes`. | Proper `#nullable enable`, **but every ref prop nullable incl. required** (`Rate.cs:22,30`) — ignores `required`. |
| **Reviewable / vendorable** | High — single self-contained file, no deps to audit. | Medium — small generated surface, but behaviour in `Microsoft.Kiota.*` deps. |
| **Wrap w/ our adapter** | Easy — POCOs + simple client to delegate to. | Easy — fluent client + POCOs; mapper reads plain props. |
| **Map to internal contracts** | Easiest — anemic POCOs, nothing to strip. | Easy — props are plain; ignore the `IParsable` plumbing when mapping. |
| **Unmapped-field capture** | `[JsonExtensionData] AdditionalProperties` every DTO (`:358`). ✅ | `IAdditionalDataHolder.AdditionalData` every model (`Rate.cs:16`). ✅ |
| **Runtime deps** | **None** (STJ in framework). | `Microsoft.Kiota.Bundle 2.0.0` (+transitive) in request path. |
| **AOT / trim** | Reflection-based STJ (works; not trim-ideal without config). | **No-reflection `IParsable`** → AOT/trim-friendly. |
| **Input formats** | OpenAPI/Swagger only. | OpenAPI only. (*Neither ingests WSDL/docs — that gap stays ours; cf. APIMatic from Phase 0.*) |

---

## 4. Risks & limitations

**NSwag**
- Default nullable quality is poor (warning-suppressed); enable `/GenerateNullableReferenceTypes` for the real generator and re-verify.
- Generated enums throw on unknown values (no tolerant fallback) — a real carrier adding an enum member would break deserialization; needs a custom converter or tolerant policy.
- No retry/timeout in generated code — relies on us wiring the `HttpClient` pipeline correctly.
- NJsonSchema/Newtonsoft appears in the banner even for the STJ path (cosmetic — STJ is used in code).

**Kiota**
- Runtime dependency on `Microsoft.Kiota.*` — counter to a fully dependency-free owned package; acceptable (MS-maintained, MIT) but must be a conscious choice.
- Ignores `required` → all reference properties nullable; our mapper must enforce required-ness itself.
- Real HTTP behaviour is in the library, so "review the generated client" reviews less than it appears.
- Fluent path-builder shape is less convenient to wrap 1:1 with a single adapter method than NSwag's flat client.

**Both**
- OpenAPI-only. The Universal connector's non-OpenAPI inputs (WSDL, Postman, raw docs) are unsolved by either — that normalization stays our responsibility (Phase 0 already flagged APIMatic's "normalize-any-format-to-OpenAPI" as the pattern to borrow).
- Tested against a **mock** spec; behaviour against a messy real carrier spec (vendor extensions, `oneOf`/`allOf`, auth schemes) is unverified — that is the next spike's job.

---

## 5. Decision notes

**Clear**
- Both install in one command, generate, and build clean on net9.
- Both preserve unmapped fields and both handle `decimal` money correctly.
- NSwag = dependency-free single-file reviewable client; Kiota = dependency-backed multi-file client with stronger auth/middleware and AOT story.
- For the first proof case the priority order (owned → reviewable → dependency-light → easy to map) points to **NSwag**.

**Unknown / to verify in the carrier spike**
- Behaviour on a **real** carrier spec: `oneOf`/`allOf`/`anyOf`, vendor extensions, multiple auth schemes, large schemas.
- NSwag with `/GenerateNullableReferenceTypes` + a tolerant enum converter — does nullable/enum quality become good enough?
- Polly retry on the NSwag `HttpClient` vs Kiota's built-in middleware — real ergonomics difference once auth+retry are both required.
- How cleanly each client wraps into our `IRateProvider`/adapter + request/response mapper with an **unresolved-mapping log**.

**Manual checks for the spike**
- Generate from one real carrier spec (UPS OpenAPI / AusPost REST / Canpar WSDL→OpenAPI from the Phase-0 carrier research) with NSwag, hand-write the adapter+mapper to our canonical model, and **log every ambiguity** — that log is the spec for what the generator must later detect.

---

## MINIMAL_IMPLEMENTATION_GATE
- plugin active: yes — `ponytail@ponytail` v4.4.0, mode reported by SessionStart hook.
- mode used: full.
- larger solution avoided: did not build a generator, CLI, abstraction layer, real-carrier integration, or a test harness; did not write an `IClientGenerator` interface around the two tools (only one consumer — the spike — exists; YAGNI).
- minimal solution chosen: install both as global tools, generate from one mock spec, add a 3-line csproj each, `dotnet build` to prove compilation, read the output, compare. Reused the Phase-0 carrier list instead of re-researching carriers.
- why enough for this spec: the gate asks which tool is the better client-layer baseline; install + generate + build + read answers that with file:line evidence. No abstraction is needed to choose a baseline.
- deferred upgrade path: if we later support both tools, introduce a thin client-generation seam **then** (option C); enable NSwag NRT + tolerant enum converter at real-generator time; revisit Kiota if AOT/trim or many-provider auth diversity become requirements.
