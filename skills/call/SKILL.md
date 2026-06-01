---
name: call
description: Place an outbound phone call through your Vocal Bridge agent (Paid subscribers only).
allowed-tools: Bash
---

Place an outbound phone call through the voice agent.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

## Usage

```bash
vb call <phone_number> [--name NAME] [--var KEY=VALUE]... [--json]
```

Phone numbers must be in E.164 format (e.g., `+14155551234`).

## Examples

```bash
# Place a call
vb call +14155551234

# With callee name
vb call +14155551234 --name "John Smith"

# Output as JSON
vb call +14155551234 --json
```

### Dynamic variables (Vapi/Retell-style `{{var}}` substitution)

Pass per-call variables that get interpolated into the agent's prompt and greeting at runtime. Mirrors Vapi/Retell's dynamic-variable model.

```bash
vb call +14155551234 \
  --var customer_name=Jane \
  --var appointment_time=3pm \
  --var order_id=ORD-123
```

Then in the agent's prompt: `You are calling {{customer_name}} to confirm their {{appointment_time}} appointment for order {{order_id}}.`

**Limits**: up to 50 variables, ≤64-char keys, ≤1024-char values, ≤8KB total. Keys must match `^[A-Za-z_][A-Za-z0-9_]*$`.

**⚠️ Security**: variable values are interpolated *verbatim* into the LLM's system prompt. If you pipe end-user-supplied data into `--var` (e.g., a name from a public web form), an attacker can inject prompt-modifying text. Treat values as trusted-equivalent to your own prompt — sanitize end-user input first.

### Mid-call control

The response includes a `token` that is **subscribe-only** — you can join the call via WebRTC to listen, but cannot publish media or forge `client_actions` events. To inject mid-call events from your backend, use `/vocal-bridge:call-action` or `/vocal-bridge:session-action`.

## Requirements

- Agent must have outbound calling enabled (`vb config set --outbound-enabled true --accept-outbound-tos`)
- Active Pilot subscription required
- Outbound Calling Terms of Use must be accepted

## Rate Limits

- 50 calls/day per agent
- 200 calls/day per user

## Based on $ARGUMENTS

- Contains a phone number (starts with +) -> use it as the destination
- Contains `--name` -> pass callee name
- Contains `--var KEY=VALUE` (repeated) -> pass per-call dynamic variables
- Contains `--json` -> output as JSON
- Empty or no phone number -> show usage instructions
