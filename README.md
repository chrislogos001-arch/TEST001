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
