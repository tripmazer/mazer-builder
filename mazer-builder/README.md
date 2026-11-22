# Mazer Builder

Build travel apps with the [Mazer API](https://tripmazer.com/developers) in Claude. The Mazer API runs finished travel jobs for your app: watch a flight until it lands, make booking documents, answer which lounges a card opens, turn a travel video into places on a map.

## What's in it

- **Three skills**, plain text that helps Claude write your code:
  - `mazer-api`: how to call the API from your own server. Keys and modes, starting jobs, safe retries, errors, and every job's input.
  - `mazer-webhooks`: receive job and flight events, and check their signatures, in Node or Python.
  - `mazer-from-amadeus`: find Amadeus Self-Service calls in a codebase and move the ones the Mazer API covers.
- **The Mazer connector**, a remote MCP server at `https://mcp.tripmazer.com/directory`, run by TripMazer. Its tools are Airport brief, Booking documents, Trip story and Check on me, plus two that read their status. The API's other jobs aren't tools here; your code calls them.

The first time you use a tool, you sign in to TripMazer in your browser, choose your account, test or live, and the most it can spend in a day. Test mode returns samples and never charges. The skills need no account.

## Install

```
/plugin marketplace add tripmazer/mazer-builder
/plugin install mazer-builder@tripmazer
```

## Try it

- "Add flight watching to our bookings with the Mazer API, test mode first."
- "Write a webhook endpoint for Mazer job events and verify the signature."
- "Which of our Amadeus calls can move to Mazer? Show me the list before changing anything."

## What it runs and sends

The plugin runs no scripts and no hooks, and reads no credentials from your machine. The skills are text. The connector receives only the inputs of the tools Claude calls, at `mcp.tripmazer.com`; it doesn't see your code or the rest of the conversation. Live jobs use your TripMazer account's prepaid credit, up to the daily limit you chose when you connected, and are charged only when they finish. Check on me messages only people who opted in through their own link.

How long results are kept is in the [privacy policy](https://tripmazer.com/legal/privacy). Disconnect any time in the [developer console](https://tripmazer.com/developers/console/keys), under Connected apps.

[Documentation](https://docs.tripmazer.com/api) · [Privacy](https://tripmazer.com/legal/privacy) · [Terms](https://tripmazer.com/legal/terms) · [Support](https://tripmazer.com/contact)
