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
