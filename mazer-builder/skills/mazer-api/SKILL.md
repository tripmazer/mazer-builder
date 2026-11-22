---
name: mazer-api
description: Integrate the Mazer API (TripMazer's travel jobs API) into an app. Use when code should start or read Mazer jobs, such as watching a flight, making a trip reel, calling a business, making booking documents or an audio guide, or when someone mentions the Mazer API, tm_test_ or tm_live_ keys, or docs.tripmazer.com/api.
---

# Mazer API

One POST starts a job. Mazer does the work on its side. The result comes back in the response, by polling, or by signed webhook. A job is charged only when it finishes; a failed job costs nothing.

## Rules

- Keep the key on a server. Never put `tm_live_` or `tm_test_` in browser or mobile code, a repository or a log. Read it from the environment.
- Build against a **test** key first. Test jobs finish at once with sample results and never charge. Same address, same shapes.
- Send an `Idempotency-Key` on every POST that starts a job, made from something stable in your own data (a booking id), so a retry can't start a second job.
- Never invent a job, a field or a price. Read `GET /v1/openapi.json` (no key needed) for shapes and `GET /v1/prices` for prices.
- Before running anything with a live key, say what it will cost and ask.

## Basics

Base address: `https://wiunqzjefneakwtlbgpp.supabase.co/functions/v1/agent-api`. Keep it in one constant.

```js
const BASE = "https://wiunqzjefneakwtlbgpp.supabase.co/functions/v1/agent-api";

export async function startJob(kind, input, idempotencyKey) {
  const res = await fetch(`${BASE}/v1/jobs`, {
    method: "POST",
    headers: {
      Authorization: `Bearer ${process.env.TRIPMAZER_KEY}`,
      "Content-Type": "application/json",
      "Idempotency-Key": idempotencyKey,
    },
    body: JSON.stringify({ kind, input }),
  });
  const body = await res.json();
  if (!res.ok) throw Object.assign(new Error(body.error?.message ?? "Mazer request failed"), { code: body.error?.code, status: res.status });
  return body; // { id, status: "queued" | "running" | "succeeded" | "failed", result, error, charged }
}
```

- `202` with `status: "queued"` is normal. Add `?wait=30` (up to 60) to hold the response, poll `GET /v1/jobs/{id}`, or use a webhook. Prefer webhooks for jobs that take minutes (reels, audio guides, calls) and for watched trips.
- Watched trips have their own resource: `POST /v1/trips`, `GET /v1/trips/{id}`, `POST /v1/trips/{id}/options`.

## Errors to handle

| Code | Do |
| --- | --- |
| `400 invalid_input` | Fix the input; the message says what |
| `401 unauthorized` | The key is wrong or revoked |
| `402 insufficient_balance`, `402 daily_cap_reached` | Stop, tell an operator; don't retry in a loop |
| `409 idempotency_key_reused` | Same key, different request: a bug in how the key is made |
| `429 rate_limited` | Back off; 120 requests a minute a key |
| `500 internal` | Retry with the same Idempotency-Key |

A job that ends `failed` has `error.code` and `error.message`, and was not charged.

## Jobs

Read [references/jobs.md](references/jobs.md) for each job's kind and what to send.
