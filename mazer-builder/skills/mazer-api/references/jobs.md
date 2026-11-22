# Mazer jobs

Shapes can grow. `GET /v1/openapi.json` is the authority; each job's page is under https://docs.tripmazer.com/api.

| Kind | Send | Get |
| --- | --- | --- |
| `watch_trip` (`POST /v1/trips`) | `flights: [{ number, date }]`, or `text`, `email` or `pdf` of an itinerary; optional `traveller`, `reference` | A trip, then events: `flight.delayed`, `flight.gate_changed`, `flight.cancelled`, `flight.diverted`, `flight.departed`, `flight.landed`, `trip.options`, `trip.completed` |
| `reel_to_trip` (`POST /v1/reels`) | `url` of an Instagram reel, TikTok or YouTube video | `destination`, `places[]` with coordinates and where in the video each appears |
| `call_business` | `business`, `on_behalf_of`, `questions[]`; optional `booking`, `facts`, `language`, `voice` | `answers[]`, `transcript`. It never books or pays |
| `trip_reel` | `destination`, `photos[]`, `clips[]`; optional `seconds`, `aspect`, `style`, `music`, `brand` | `video_url` (download it: links expire) |
| `trip_story` | `destination`, `days[]`; optional `photos`, `notes`, `voice`, `length`, `language` | `title`, `days[]`, `markdown` |
| `audio_guide` | `destination` with `stops[]`, or `days[]`; optional `language`, `interests` | `chapters[]`, `episode_url`, `feed_url` |
| `booking_documents` | `kind: "flight" or "hotel"`, `booking`; optional `wallet` | `pdf.url`, `wallet.save_url` |
| `weather_plan_b` | `city` or `lat` and `lng`, `date`, `stops[]` | `risk`, `changes[]`, `forecast[]` |
| `airport_brief` | `airport`, optional `cards[]`, `terminal`, `side` | `lounges[]` with a verdict each, terminals, notes |
| `route_india` | `from`, `to`, `date`; optional `adults` | `picks` (fastest, cheapest, overnight), `flights`, `trains`, `buses` |
| `check_on_me` (`POST /v1/checkins`) | `traveller`, `contacts[]`, `minutes` or `due_at` | Opt-in links, then `checkin.due`, `checkin.alert`, `checkin.fine`, `checkin.all_clear` |

Rules people miss:

- `call_business` phones a real business on a live key. The number must be on Google Maps or the business's own website, or printed in the booking you send.
- `check_on_me` messages only people who open their own link. Show each person theirs.
- `trip_reel` needs the rights to the photos, clips and any music you send. Set `music.rights_confirmed` only when that is true.
