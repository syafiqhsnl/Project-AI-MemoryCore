# Per-Endpoint API Rate Limiting
*Preventing resource exhaustion and scraper abuse via granular request throttling*

## Threat Model
- **Threat**: Denial of Service (DoS) and excessive scraping of high-compute endpoints.
- **Impact**: Backend services (like OpenTripPlanner) can be overwhelmed by a single heavy user, causing downtime for all other clients.
- **Severity**: High (Production critical)

## Use Case
- Public APIs where some endpoints are vastly more expensive than others (e.g., pathfinding vs. simple lookups).
- Systems requiring per-client (IP-based) isolation to prevent "noisy neighbor" issues.

## Implementation

### Backend (Laravel RateLimiter)
```php
// app/Providers/AppServiceProvider.php
RateLimiter::for('find-route', function (Request $request) {
    return Limit::perMinute(config('transport-api.rate_limit.find_route_per_minute', 10))
        ->by($request->ip());
});

RateLimiter::for('journey', function (Request $request) {
    return Limit::perMinute(config('transport-api.rate_limit.journey_per_minute', 20))
        ->by($request->ip());
});
```

### Middleware Assignment
```php
// routes/api.php
Route::middleware('throttle:find-route')->group(function () {
    Route::get('/routes', [TransportController::class, 'findRoute']);
});

Route::middleware('throttle:journey')->group(function () {
    Route::post('/journey/plan', [JourneyPlannerController::class, 'planJourney']);
});
```

## Configuration

### Environment Variables
```env
API_RATE_LIMIT=60
API_RATE_LIMIT_FIND_ROUTE=10
API_RATE_LIMIT_JOURNEY=20
```

### Config Files
```php
// config/transport-api.php
'rate_limit' => [
    'per_minute'            => env('API_RATE_LIMIT',            60),
    'find_route_per_minute' => env('API_RATE_LIMIT_FIND_ROUTE', 10),
    'journey_per_minute'    => env('API_RATE_LIMIT_JOURNEY',    20),
],
```

## Validation Rules
- [Per-IP Keying]: Must use `->by($request->ip())` to ensure isolation.
- [Config Defaults]: closures must provide sensible defaults if config is missing.

## Error Responses
- [Quota Exhausted]: `HTTP 429 Too Many Requests`
- [Headers]: Must include `Retry-After`, `X-RateLimit-Limit`, and `X-RateLimit-Remaining`.

## Testing

### Test Cases
- [Isolation Test]: Verify IP-A being blocked does NOT block IP-B.
- [Independence Test]: Verify exhausting the expensive limiter does NOT exhaust the global limiter.
- [Header Verification]: Assert `Retry-After` is a positive integer on 429 response.

### Security Audit Checklist
- [x] Are all sensitive/expensive endpoints covered by a specific limiter?
- [x] Is the keying uniquely identifying the client (e.g., IP or User ID)?
- [x] Do the 429 responses leak sensitive information? (No)

## Logging & Monitoring
- Monitor for spikes in 429 responses in app logs to detect potential brute force or misconfigured scrapers.

## Projects Using This
- **transport-api** (v3.0.0 Maintenance)

---
*Documented: 2026-04-12*
