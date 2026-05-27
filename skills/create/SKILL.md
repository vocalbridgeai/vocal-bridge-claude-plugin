---
name: create
description: Create and deploy a new voice agent (paid subscribers only)
allowed-tools: Bash, Read, Write
---

Create and deploy a new Vocal Bridge voice agent.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

## Usage

```bash
vb agent create --name "Agent Name" --style Chatty --prompt "Your system prompt"
```

## Required Parameters

- `--name` - Agent name
- `--style` - Agent style: Chatty, Focused, Gemini, Ultravox, or Listener
- `--prompt` or `--prompt-file` - System prompt (text or file path)

## Optional Parameters

- `--greeting` - Greeting message
- `--deploy-targets` - phone, web, or both (default: web). Subscribe to Pilot to deploy on phone numbers
- `--background-enabled true|false` - Enable background AI (default: true)
- `--web-search-enabled true|false` - Enable web search (default: true)
- `--hold-enabled true|false` - Enable hold capability (default: false)
- `--hangup-enabled true|false` - Enable hangup capability (default: false)
- `--debug-mode true|false` - Enable debug mode (default: false)
- `--model-settings-file` - JSON file with model settings
- `--mcp-servers-file` - JSON file with MCP servers array
- `--json` - Output as JSON

## Listener-Mode Parameters (only meaningful when `--style Listener`)

- `--coachee-description TEXT` - Names the person you're coaching. When set, the agent only generates a coaching card when *someone else* is speaking, and tailors each card as guidance for them. Example: `"the CFO during earnings Q&A"`.
- `--coaching-debounce SECS` - Minimum seconds between coaching cards (0-60, default 12). Higher = more spaced-out suggestions.
- `--coaching-context-turns N` - Recent turns of conversation the agent considers per coaching card (0-50, default 10).
- `--coaching-job-timeout SECS` - Max seconds to wait for each coaching card before giving up (5-120, default 30).
- `--coaching-gate true|false` - When `true` (default), coaching only appears when the conversation matches the trigger conditions in your prompt. When `false`, every spoken turn produces a coaching card.
- `--speaker-map true|false` - When `true` (default), the agent identifies who's speaking and emits `speaker_map_update` events. When `false`, your app only sees raw labels like `S0`, `S1`.
- `--speaker-map-interval SECS` - How often to re-check speaker identities (5-300, default 20).

## Based on $ARGUMENTS

Parse user intent from $ARGUMENTS:
- If user provides agent details inline -> construct `vb agent create` command with appropriate flags
- If user provides a prompt file path -> use `--prompt-file`
- If user wants to create from a description -> write prompt to a temp file, then use `--prompt-file`
- If user specifies a style/mode -> use `--style`

## Examples

```bash
# Create a simple chatty agent
vb agent create --name "My Assistant" --style Chatty --prompt "You are a helpful assistant."

# Create with a greeting
vb agent create --name "Sales Bot" --style Focused --prompt "You help customers find products." --greeting "Hi! How can I help you today?"

# Create agent (web is the default)
vb agent create --name "Web Agent" --style Chatty --prompt "You are a support agent."

# Create from prompt file
vb agent create --name "Custom Agent" --style Focused --prompt-file prompt.txt

# Create with model settings
vb agent create --name "Custom Voice" --style Focused --prompt "You are helpful." --model-settings-file settings.json

# Create a Listener (passive observer — never speaks, streams transcripts and
# coaching suggestions to your app via the data channel)
vb agent create --name "IR Coach" --style Listener \
  --prompt "You coach during earnings Q&A. Trigger on analyst questions. Respond with a bold recommended answer, 2-3 supporting bullets, and one italic caveat." \
  --coachee-description "the company CFO during earnings Q&A" \
  --coaching-debounce 8
```

## Style picking guide

- **Chatty / Focused / Gemini / Ultravox** — bidirectional voice conversation. Agent listens, thinks, and speaks back.
- **Listener** — passive observer. Joins a session, transcribes multi-speaker audio with diarization, runs your `custom_prompt` as a coaching policy, and pushes coaching suggestions to the client app's data channel. Never speaks. Web-only (no phone number). Use this when the user describes a "live coaching", "real-time assist", "watch-the-meeting", or "transcribe and suggest" use case.

## Notes

- Requires an active paid subscription
- Maximum 50 total agents per account
- Agent will be deployed to web by default. Subscribe to Pilot to deploy on phone numbers
- After creation, create an API key in the dashboard to manage the new agent via CLI
