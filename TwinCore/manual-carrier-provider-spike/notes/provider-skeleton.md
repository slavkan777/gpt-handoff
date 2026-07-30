# UPS Rate Provider — skeleton NOTES (NOT implemented)

Design sketch only, per Phase 0.2 step 7 (generation was stable). No code is written/committed for these — this file records the intended shape and, more importantly, the **mapping ambiguities** the future generator must detect and log. Canonical type names match the G1 vertical slice (`IRateProvider`, `RateQuoteRequest`, `RateQuoteResult`, `QuoteOption`, `MoneyValue`).

The generated client (`../generated/UpsRatingClient.cs`) is the **bottom layer**. These six types are the layer we own *above* it.

## 1. IRateProvider (canonical, carrier-agnostic — already exists in G1)
```
Task<RateQuoteResult> GetRatesAsync(RateQuoteRequest request, CancellationToken ct);
```

## 2. UpsRateProvider : IRateProvider
Orchestrates: `request → UpsRateRequestMapper → Client.RateAsync(...) → UpsRateResponseMapper`; on `ApiException<ErrorResponse>` → `UpsErrorMapper`. Holds the generated `IClient` (DI-injected typed `HttpClient`) + `UpsRateOptions`.

## 3. UpsRateRequestMapper : RateQuoteRequest → RATERequestWrapper
Ambiguities to log:
- canonical `serviceCode` → UPS `requestoption` (Rate/Shop/Ratetimeintransit/Shoptimeintransit) is **not 1:1** — needs a decision table.
- weight/dimension **units**: canonical enum (lb/kg, in/cm) → UPS `UnitOfMeasurement.Code` strings.
- `version` path segment (`v2409`) is an API-release knob, not business data → comes from options, not the request.

## 4. UpsRateResponseMapper : RATEResponseWrapper → RateQuoteResult (QuoteOption[])
Ambiguities to log (**the important ones**):
- **Money is `string MonetaryValue`, not a number** (`UpsRatingClient.cs:2613` and ~20 more). Mapper must parse `string → decimal` with `InvariantCulture` and **fail loudly** on unparseable values — never silently default to 0.
- **Currency is a separate sibling** (`CurrencyCode`), conditionally required → must be **joined** with the amount to build canonical `MoneyValue(amount, currency)`.
- Multiple charge buckets (TotalCharges / ItemizedCharges / negotiated `NegotiatedRateCharges`) → which maps to `QuoteOption.Price`? Decision needed; log the unmapped buckets.
- deep envelope `RATEResponseWrapper.RateResponse.RatedShipment[]` → flatten to `QuoteOption[]`.

## 5. UpsErrorMapper : ApiException / ApiException<ErrorResponse> → RateQuoteError
Generated client already separates statuses (`UpsRatingClient.cs:243–281`): 400 Invalid Request, 401 Unauthorized, 403 Blocked Merchant, 429 Rate Limit. Map → canonical categories (validation / auth / throttle / transport). UPS `ErrorResponse.response.errors[].code+message` → canonical error list.

## 6. UpsRateOptions
- `BaseUrl` (default `https://wwwcie.ups.com/api` = CIE test; prod `https://onlinetools.ups.com/api`).
- OAuth2 client id/secret → **from secret store / env at runtime, never committed**; token acquisition + bearer injection via an `HttpClient` `DelegatingHandler` (the generated client has no auth — `PrepareRequest` partial / handler is the seam).
- `Version` (e.g. `v2409`), request timeout, Polly retry policy (esp. for 429).

## Why this is the generator spec
Every "ambiguity to log" above is something NSwag could **not** infer from the spec — string-money, split currency, charge-bucket choice, requestoption mapping. A real generator must emit an **unresolved-mapping log** listing exactly these, plus a review checklist, rather than guessing.
