# NSwag vs Kiota — Tool Trial Working Notes

Lab notebook for Phase 0.1. Raw evidence only; the structured report is `../../tool-trial-nswag-kiota.md`.

## Environment
- Windows 10, dotnet SDK **9.0.304** (8.0.422 also present).
- **NSwag.ConsoleCore 14.7.1** (cmd `nswag`) — `dotnet tool install --global NSwag.ConsoleCore`
- **Microsoft.OpenApi.Kiota 1.32.2** (cmd `kiota`) — `dotnet tool install --global Microsoft.OpenApi.Kiota`
- Both installed clean (no prior version). Global-tool install only; nothing touched outside the trial folder.

## Test input
- `input/openapi.json` — **SYNTHETIC** mock carrier Rate API (OpenAPI 3.0.3). NOT a real carrier, no endpoint, no credentials, no live call.
- Deliberately exercises: nested object (`Address`), arrays of objects (`packages`, `rates`), required-vs-optional, two inline enums (`lb|kg`, `in|cm`), money as `number/format:decimal`, both 200 and 400 responses.
- Mock is acceptable per the gate (tool-shape eval needs no real spec) and avoids carrier-login / licensing friction. Flagged as mock in the report.

## Commands (exact)
NSwag:
```
nswag openapi2csclient /input:input/openapi.json /classname:MockCarrierClient /namespace:ToolTrial.NSwagClient /output:nswag-output/MockCarrierClient.cs /jsonLibrary:SystemTextJson /generateClientInterfaces:true
```
Kiota:
```
kiota generate -l CSharp -d input/openapi.json -o kiota-output -n ToolTrial.KiotaClient -c MockCarrierClient --clean-output
```
Both exit 0.

## Output shape
- **NSwag:** 1 file, 513 lines, 30.5 KB — interface + client + all DTOs + enums + `ApiException` in one file.
- **Kiota:** 13 files, 923 lines — root client + `Models/` (8 model + 2 enum files) + `Rates/RatesRequestBuilder.cs` + `kiota-lock.json`.

## Build (proof both compile)
Minimal net9 classlib added per output (`NSwagTrial.csproj`, `KiotaTrial.csproj`).
- **NSwag:** no extra NuGet (System.Text.Json in framework). **Build succeeded, 0 Warning / 0 Error, exit 0.**
- **Kiota:** + `Microsoft.Kiota.Bundle 2.0.0` (per `kiota info`). **Build succeeded, 0 Warning / 0 Error, exit 0.**
- `bin/`,`obj/` git-ignored (`.gitignore` in trial root).

## Per-tool observations (file:line evidence)

### NSwag
- Typed-client ctor `MockCarrierClient(HttpClient)` (`nswag-output/MockCarrierClient.cs:59`) → `services.AddHttpClient<IMockCarrierClient,MockCarrierClient>()`.
- Extension seams = `partial void`: `PrepareRequest` ×2 / `ProcessResponse` / `UpdateJsonSerializerSettings` / `Initialize` (:87–93). Auth/headers via the `PrepareRequest` partial or an `HttpClient` `DelegatingHandler`.
- **No** built-in retry/timeout — layer Polly on the injected `HttpClient`.
- Errors: throws `ApiException` / `ApiException<ErrorResponse>` with `StatusCode`+`Response`+`Headers`+typed `.Result` (:174, :580–613).
- Money: `public decimal Amount` (:468) — honors `format:decimal`. ✅
- Unknown fields: `[JsonExtensionData] AdditionalProperties` on **every** DTO (:358) — round-trips unmapped JSON. ✅ (useful for unresolved-mapping detection).
- Enums: real C# enums + `[EnumMember]` + `JsonStringEnumConverter<T>` (:382, :554–576). No unknown-value fallback (STJ throws on unknown enum string).
- Nullable: **weak by default** — no `?` on ref props; blanket `#pragma warning disable 8600/8602/8603/...` (:16–21). NSwag *can* emit NRT (`/GenerateNullableReferenceTypes`); not enabled this run.
- DTOs = plain POCOs → easy to hand-map to an internal model.
- Single file, conventional `HttpClient.SendAsync` visible (:141) → fully auditable in one pass, but pragma-heavy header.

### Kiota
- Ctor `MockCarrierClient(IRequestAdapter)` (`kiota-output/MockCarrierClient.cs:31`). Auth = first-class `IAuthenticationProvider` passed to the adapter.
- Fluent API: `client.Rates.PostAsync(body, cfg, ct)` (`Rates/RatesRequestBuilder.cs:46`).
- Retry / redirect / param-decoding middleware ship in Kiota's default handler chain (in the runtime lib, not generated).
- Errors: `errorMapping {"400" → ErrorResponse}` (`RatesRequestBuilder.cs:55–59`); `ErrorResponse : ApiException` (`Models/ErrorResponse.cs:13`) → **catchable as a typed exception** carrying the parsed body.
- Money: `public decimal? Amount` (`Models/Rate.cs:18`) via `GetDecimalValue`/`WriteDecimalValue`. ✅
- Models: `IParsable`, hand-written `GetFieldDeserializers`/`Serialize` (`Rate.cs:74–100`) — **no reflection** → AOT/trim-friendly and fast.
- Unknown fields: `IAdditionalDataHolder.AdditionalData` (`Rate.cs:16`). ✅
- Nullable: proper `#nullable enable` annotations, **but every ref prop is nullable incl. required** `CarrierCode`/`Currency` (`Rate.cs:22,30`) — Kiota ignores OpenAPI `required` for nullability.
- The actual HTTP send lives in `Microsoft.Kiota` runtime (`RequestAdapter.SendAsync`) — less generated code to review, but behaviour sits in a dependency.
- Multi-file + per-file `#if NETSTANDARD2_1_OR_GREATER` multitarget guards = noisier per file.
