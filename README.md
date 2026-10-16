# Stateless Customer-Service Widget

Static, dependency-free iframe widget. No cookies, localStorage, sessionStorage, history or fingerprinting; `noindex` meta; `no-referrer`.

## Files
- `widget.html` – the iframe page (all content and JS inline).
- `embed-snippet.html` – iframe tag to paste into the host site.

## Configure
Set the HTTPS feedback endpoint either by editing `ENDPOINT` in `widget.html`, or via the iframe URL: `widget.html?endpoint=<url-encoded https URL>` (only `https://` is accepted).

## Endpoint contract (one-way feedback)
Request: `POST`, `credentials: omit`, no Referer.

Headers: `Content-Type: application/json;charset=utf-8`, `X-Widget-Channel: oneway-feedback`, `X-Widget-Mode: stateless`

Body:
```json
{ "channel": "oneway-feedback", "message": "<cleaned text, max 2000 chars>", "length": 13, "ts": 1791835000365 }
```

The custom headers trigger a CORS preflight. The endpoint must answer `OPTIONS` and `POST` with:
```
Access-Control-Allow-Origin: https://YOUR-DOMAIN
Access-Control-Allow-Methods: POST, OPTIONS
Access-Control-Allow-Headers: Content-Type, X-Widget-Channel, X-Widget-Mode
```
Any 2xx response is treated as delivered; the textarea is then cleared.

## Order requests (tabs ❽ / 🔟 / ⓫)
Each "單次特許解鎖" click sends one independent request to the same endpoint (repeatable, nothing retained client-side):

Header `X-Widget-Channel: order-request`, body:
```json
{ "channel": "order-request", "sku": "gateway-configuration-guide", "priceUsd": "19.00", "quantity": 1, "ts": 1791837562250 }
```
SKUs: `commercial-data-assistant` ($9.00), `gateway-configuration-guide` ($19.00), `corporate-documentation-kit` ($29.00). The widget does not take payment; the endpoint must hand off to a payment processor.

## Anti-Crawler Mini-Widget (`anti-crawler-widget.html`)
Standalone sub-module, linked from the bottom of `widget.html`. The existing tabs and the $99 package are unchanged. Same statelessness rules: no storage or cookies, storage is purged on load, and `pagehide`/`beforeunload` drops the in-memory log and stops the clock.

URL parameters:
- `endpoint=<https URL>` – same as `widget.html`.
- `lang=zh|en` – initial language (default follows `navigator.language`); the 中文/EN buttons switch it in place.
- `expires=YYYY-MM-DD` – plan expiry (23:59:59 UTC). Drives the countdown. A reminder is shown once 5 days remain and replaced by a second reminder once 3 days remain; after expiry an "expired, re-purchase manually" notice is shown. There is no persistence, so the issuer must put the expiry in the URL.

Order request (one-time, non-recurring):
```json
{ "channel": "order-request", "sku": "anti-crawler-mini-widget", "priceUsd": "9.90", "quantity": 1, "recurring": false, "lang": "en", "ts": 1791837562250 }
```
Block Details Tracking only lists events the page observes about itself (load, storage purge, being embedded, the order request). It does not scan or probe anything outside the page.
