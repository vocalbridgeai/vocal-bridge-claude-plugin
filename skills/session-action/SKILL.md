---
name: session-action
description: Inject a configured app_to_agent client_actions event into a live call session (inbound phone call or web session).
allowed-tools: Bash
---

Inject an `app_to_agent` `client_actions` event into a live call session. Unlike `/vocal-bridge:call-action`, this keys on `call_sessions.id` (the session ID you see in `vb logs`), so it works for **inbound phone calls and web sessions** — anywhere there's no outbound call log.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

## Usage

```bash
vb session-action <session_id> <action> [--payload JSON] [--payload-file PATH] [--json]
```

- `<session_id>` — call session ID (from `vb logs` or `vb logs list`).
- `<action>` — name of a configured `app_to_agent` action on the agent.
- `--payload` — JSON object/array passed verbatim to the agent.
- `--payload-file` — read the payload from a file instead.

## Examples

```bash
# Find the live session ID for a current call
vb logs list --status in_progress

# Inject an event
vb session-action 550e8400-e29b-41d4-a716-446655440000 appointment_confirmed
vb session-action <session_id> form_submitted --payload-file form.json
vb session-action <session_id> user_clicked_buy \
  --payload '{"product_id":"ABC","quantity":2}'
```

## Requirements

- The action must be a configured `app_to_agent` action on the agent. Configure via `vb config set --client-actions-file actions.json`.
- The session must still be live (`status` is `in_progress`).
- Your API key must be scoped to the agent that owns the session.

## Rate Limits

- 20/min per IP
- 120/hour per API key
- 2000/day per API key

## When to use this vs. `/vocal-bridge:call-action`

- `session-action` keys on `call_sessions.id` (works for inbound + web + outbound).
- `call-action` keys on the outbound `call_id` returned by `vb call`. Use it when you've just placed an outbound call and already have the call_id in hand.

## Based on $ARGUMENTS

- First positional argument -> `session_id`
- Second positional argument -> `action` name
- Contains `--payload` -> use the JSON string verbatim
- Contains `--payload-file` -> read JSON from that path
- Contains `--json` -> output raw JSON
- Missing session_id or action -> show usage
