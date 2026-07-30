# Rate Limit Tester

Probe a URL with N requests per second from your browser. See when it 429s, what `Retry-After` it returns, and how rate-limit headers behave.

**Live demo:** https://0xelitesystem.github.io/rate-limit-tester/

## Use

Open [`index.html`](./index.html). Enter a URL, configure RPS and duration, click Start. The tool sends requests at the configured rate and shows:

- 6 live gauges (total sent, 2xx, 429s, errors, avg latency, first 429 request number)
- Color-coded timeline (green/blue/red/orange/yellow/grey by status class)
- Per-request table with `Retry-After` and `X-RateLimit-*` headers

## Why this exists

Most rate-limit issues are answered by these questions:

- At what RPS does my service start returning 429?
- Does my service return `Retry-After`? With what value?
- Do my `X-RateLimit-*` headers match what I think they do?
- Does the third-party API I'm integrating respond predictably under burst load?

A browser-based tester answers them in seconds. No setup, no scripts, no commitment.

## When this is useful

- Validating rate limits on your own staging API before deploy
- Characterizing third-party rate limits before integration (where allowed)
- Demonstrating a bug to a backend engineer ("send 20 RPS for 5 seconds, see what comes back")
- Teaching what 429 / Retry-After / X-RateLimit headers mean

## When this is NOT enough

- **Not for serious load testing.** Browser fetch is throttled by your machine and connection. For real load tests, use k6, vegeta, wrk, or hey.
- **Not for distributed testing.** Single-source. Can't simulate traffic from multiple regions.
- **Not for testing your own service from inside your VPN.** CORS rules apply.
- **Not for stress-testing third parties without permission.** This is hostile traffic. Don't.

## CORS

Browsers enforce CORS. Most third-party APIs don't allow cross-origin requests from a random page, so requests will fail (you'll see them as errors, not 429s). For your own services, add the appropriate `Access-Control-Allow-Origin` header on the endpoint, or run the tester served from the same origin.

This is intentional. Browser-based load testing without CORS protection would be an abuse vector.

## Privacy

All requests originate from your browser to the configured URL. No requests go to any other server. The tool itself is a single static HTML file with no analytics.

## Run locally

```bash
git clone https://github.com/0xelitesystem/rate-limit-tester
cd rate-limit-tester
# Open index.html, or:
python -m http.server 8000
```

## Reading the results

- **First 429 at request #N**, your service starts limiting after N successful requests in the configured window
- **Avg latency increasing during the run**, service is queueing requests, may be approaching capacity
- **Mix of 429 and 5xx**, limits are kicking in but also something else is breaking under load
- **All errors / no responses**, likely CORS blocking; check browser console
- **Retry-After in seconds**, wait at least that long before retrying
- **Retry-After as HTTP date**, wait until that timestamp before retrying

## Contribute

PRs welcome:

- More header parsing (different conventions: `RateLimit-Limit`, `RateLimit-Remaining`, etc.)
- Export results as CSV or JSON
- Burst patterns (not just steady RPS)
- Better error categorization (timeout vs CORS vs DNS)

Don't add: external scripts, telemetry, package.json. Single file by design.

## Build

There is no build. Single HTML file.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT.

## Related

- [webhook-inspector](https://github.com/0xelitesystem/webhook-inspector), inspect webhook payloads
- [webhook-signature-verifier](https://github.com/0xelitesystem/webhook-signature-verifier), verify webhook signatures
- [oauth-flow-debugger](https://github.com/0xelitesystem/oauth-flow-debugger), decode OAuth URLs
