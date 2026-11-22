---
name: mazer-from-amadeus
description: Find Amadeus Self-Service API calls in a codebase and plan or make the move to the Mazer API for the ones it covers. Use when someone mentions Amadeus Self-Service, the amadeus npm or pip package, amadeus4dev, test.api.amadeus.com, or replacing Amadeus after its portal closed in July 2026.
---

# Moving off Amadeus Self-Service

Amadeus closed its Self-Service portal on 17 July 2026. The Mazer API takes over a few of its calls and none of its search, pricing or booking. Say that plainly; never move a call it doesn't cover.

## 1. Find the calls

Search for `amadeus`, `test.api.amadeus.com`, `api.amadeus.com` and these methods, and list each file and line:

`shopping.flightOffersSearch`, `shopping.flightOffers.pricing`, `booking.flightOrders`, `shopping.seatmaps`, `shopping.hotelOffersSearch`, `booking.hotelOrders`, `schedule.flights`, `travel.predictions.flightDelay`, `referenceData.locations`, `shopping.activities`, `eReputation.hotelSentiments`, `shopping.transferOffers`.

## 2. Sort them, and show the list before changing anything

| Call | Moves to |
| --- | --- |
| `schedule.flights` (On Demand Flight Status) | `POST /v1/trips`: watch the flight once, receive events. Remove the polling |
| `travel.predictions.flightDelay` | `POST /v1/trips` in part: reported delays, no prediction. Ask whether that is enough |
| Showing a booked flight or hotel | `booking_documents`: e-ticket or voucher PDF, Wallet pass |
| Points of interest or activities shown as content | `audio_guide` or `reel_to_trip`, only if the feature is content and not a searchable list |
| Anything that searches, prices, books, rates or predicts | Not covered. Leave it, and say a booking or data API is needed |

## 3. Move one, in test mode

Use the mazer-api skill for the call and mazer-webhooks for events. Keep the old interface your app calls; replace what is behind it. Turn "ask for the status now" into "read the latest event we stored", and store each event as it arrives. Open a simulated trip with a test key and watch a gate change, a delay, take-off and landing arrive before touching live.

Remove the Amadeus client id, secret and package once nothing uses them.

More: https://docs.tripmazer.com/api/moving-from-amadeus
