# Mazer Builder

Build travel apps with the [Mazer API](https://tripmazer.com/developers) in Claude Code. The Mazer API runs finished travel jobs: watch a flight until it lands, make a reel from a trip's photos, call a hotel with your questions, turn a travel video into places on a map.

## What's in it

- **The Mazer MCP server**, connected at `https://mcp.tripmazer.com/mcp`. The first time you use a tool, you sign in to TripMazer in your browser, choose your account, test or live, and the most it can spend in a day. Test mode returns samples and never charges.
- **`mazer-api`**: how to call the API from your own code. Keys and modes, starting jobs, safe retries, errors, and every job's input.
- **`mazer-webhooks`**: receive job and flight events, and check their signatures, in Node or Python.
- **`mazer-from-amadeus`**: find Amadeus Self-Service calls in a codebase and move the ones the Mazer API covers.

## Install

```
/plugin marketplace add tripmazer/mazer-builder
/plugin install mazer-builder@tripmazer
```

## Try it

- "Add flight watching to our bookings with the Mazer API, test mode first."
- "Write a webhook endpoint for Mazer job events and verify the signature."
- "Which of our Amadeus calls can move to Mazer? Show me the list before changing anything."

## What it sends

The plugin runs no scripts and no hooks, and reads no credentials from your machine. The MCP server receives only the inputs of the tools Claude calls. Live jobs use your TripMazer account's prepaid credit, up to the daily limit you chose when you connected. Disconnect it in the [developer console](https://tripmazer.com/developers/console/keys) under Connected apps.

[Documentation](https://docs.tripmazer.com/api) · [Privacy](https://tripmazer.com/legal/privacy) · [Terms](https://tripmazer.com/legal/terms) · [Support](https://tripmazer.com/contact)
