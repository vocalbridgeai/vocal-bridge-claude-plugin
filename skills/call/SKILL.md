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
vb call <phone_number> [--name NAME] [--json]
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
- Contains `--json` -> output as JSON
- Empty or no phone number -> show usage instructions
