---
name: call-action
description: Inject a configured app_to_agent client_actions event into a live outbound phone call from your backend.
allowed-tools: Bash
---

Inject an `app_to_agent` `client_actions` event into a live outbound phone call. The envelope is published on the call's `client_actions` data-channel topic — the agent processes it the same way it would a live web-client event.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

## Usage

```bash
vb call-action <call_id> <action> [--payload JSON] [--payload-file PATH] [--json]
```

- `<call_id>` — outbound call ID returned by `vb call`.
- `<action>` — name of a configured `app_to_agent` action on the agent.
- `--payload` — JSON object/array passed verbatim to the agent.
- `--payload-file` — read the payload from a file instead.

## Examples

```bash
# Place a call and capture the call_id
CALL_ID=$(vb call +14155551234 --json | jq -r .call_id)

# Inject a buy event mid-call
vb call-action "$CALL_ID" user_clicked_buy \
  --payload '{"product_id":"ABC","quantity":2}'

# Payload from a file
vb call-action "$CALL_ID" form_submitted --payload-file event.json

# Raw JSON output
vb call-action "$CALL_ID" user_clicked_buy --json
```

## Requirements

- The action must be a configured `app_to_agent` action on the agent. Configure via `vb config set --client-actions-file actions.json`.
- The call must still be live (status `initiated` or `answered`).
- Your API key must be scoped to the agent that owns the call.

## Rate Limits

- 20/min per IP
- 120/hour per API key
- 2000/day per API key

## When to use this vs. `/vocal-bridge:session-action`

- `call-action` keys on the outbound `call_id` (from `vb call`).
- `session-action` keys on `call_sessions.id` (from `vb logs`) — use it for inbound calls and web sessions, where there's no outbound call log.

## Based on $ARGUMENTS

- First positional argument -> `call_id`
- Second positional argument -> `action` name
- Contains `--payload` -> use the JSON string verbatim
- Contains `--payload-file` -> read JSON from that path
- Contains `--json` -> output raw JSON
- Missing call_id or action -> show usage
