---
name: mazer-webhooks
description: Receive Mazer API webhooks (job.succeeded, job.failed, flight.landed, trip.options, checkin.alert) and verify X-TripMazer-Signature. Use when adding, fixing or reviewing an endpoint that receives events from TripMazer's Mazer API.
---

# Mazer webhooks

Add the endpoint in the developer console under Webhooks. Its signing secret is shown once; keep it with the app's other secrets and pass it to `verify`.

## Verify first, on the raw body

Each request carries `X-TripMazer-Signature: t=<unix seconds>,v1=<hex>`. `v1` is the HMAC-SHA256 of `<t>.<raw body>` with the secret. Use the bytes as received: parsing and re-serialising JSON breaks the signature.

```js
import crypto from "node:crypto";

export function verify(rawBody, header, secret) {
  const { t, v1 } = Object.fromEntries(String(header ?? "").split(",").map((p) => p.split("=")));
  if (!t || !v1 || Math.abs(Date.now() / 1000 - Number(t)) > 300) return false;
  const expected = crypto.createHmac("sha256", secret).update(`${t}.${rawBody}`).digest("hex");
  const got = Buffer.from(v1), want = Buffer.from(expected);
  return got.length === want.length && crypto.timingSafeEqual(got, want);
}
```

```python
import hashlib, hmac, time

def verify(raw_body: bytes, header: str, secret: str) -> bool:
    parts = dict(p.split("=", 1) for p in (header or "").split(",") if "=" in p)
    t, v1 = parts.get("t", ""), parts.get("v1", "")
    if not (t.isascii() and t.isdigit()) or abs(time.time() - int(t)) > 300:
        return False
    expected = hmac.new(secret.encode(), f"{t}.".encode() + raw_body, hashlib.sha256).hexdigest()
    return hmac.compare_digest(v1.encode(), expected.encode())
```

Get the raw body: Express `express.raw({ type: "application/json" })` on this route only; Next.js `await req.text()`; FastAPI `await request.body()`; Django `request.body`.

## Then

1. Answer `2xx` within 10 seconds and do the work after. Anything else is retried up to seven times over about a day.
2. Handle each event once: the same event can arrive twice. Key it on the event name, `data.id` and `occurred_at`.
3. Switch on `event`; ignore names you don't know, new ones will come.
4. Don't trust order. A `flight.landed` can arrive before a late `flight.gate_changed`.
5. `flight.landed` can carry `inferred: true` with `estimated_arrival` and no `actual_arrival`: say "around" when you show it.
6. Test with a test key: jobs finish at once and a simulated trip plays its events over a few minutes.
