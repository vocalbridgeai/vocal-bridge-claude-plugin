---
name: config
description: View and update all agent configuration settings including style, capabilities, MCP servers, and more.
allowed-tools: Bash, Read, Write
---

Manage the voice agent's full configuration settings.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

## Commands

### Discover valid options

**IMPORTANT**: Before updating settings, use `vb config options` to discover valid values. This prevents errors from invalid values.

```bash
# Show all available options for current agent style
vb config options

# Show options for a specific setting (by name or label)
vb config options voice
vb config options "TTS Model"

# Show all settings in a category
vb config options audio
vb config options stt
vb config options realtime

# Show language options (for Chatty/Focused styles)
vb config options language

# Output as JSON
vb config options --json
```

### Show all settings

```bash
vb config show
```

Shows all agent settings in a readable format including:
- Core settings (style, deploy targets)
- Capabilities (background AI, hold, hangup, debug mode)
- Greeting and system prompt
- Model settings
- MCP servers
- Post-processing configuration
- Built-in integrations

For JSON output:

```bash
vb config show --json
```

### Export config sections

Export a specific config section as JSON. Output is pipe-friendly for roundtripping:

```bash
# Available sections: model-settings, client-actions, mcp-servers, api-tools, ai-agent, builtin-tools
vb config get model-settings
vb config get client-actions
vb config get ai-agent

# Save to file, edit, then re-apply
vb config get model-settings > settings.json
# edit settings.json...
vb config set --model-settings-file settings.json
```

### Partial updates with --merge

Use `--merge` to deep-merge file contents with current settings instead of replacing. Only the fields you specify are changed:

```bash
# Update only the model, keeping voice/VAD/everything else intact
echo '{"realtime": {"model": "gpt-realtime-1.5"}}' > update.json
vb config set --model-settings-file update.json --merge
```

`--merge` works with dict-based configs: `--model-settings-file`, `--builtin-tools-file`, `--ai-agent-file`. Array-based configs (client-actions, mcp-servers, api-tools) are always replaced.

### Update individual settings

```bash
vb config set [OPTIONS]
```

Available options:
- `--name NAME` - Agent name
- `--style STYLE` - Agent style (Chatty, Focused, Gemini, Ultravox, Listener)
- `--greeting TEXT` - Greeting message (use '' to clear)
- `--prompt TEXT` - System prompt (use '' to clear)
- `--deploy-targets TARGET` - Deploy targets (phone, web, both). Subscribe to Pilot to deploy on phone numbers
- `--background-enabled true|false` - Enable background AI system
- `--hold-enabled true|false` - Enable hold capability
- `--hangup-enabled true|false` - Enable hangup capability
- `--debug-mode true|false` - Enable debug event streaming
- `--post-processing-prompt TEXT` - Post-processing prompt
- `--post-processing-mcp-url URL` - Post-processing MCP server URL
- `--model-settings-file FILE` - JSON file with model settings
- `--mcp-servers-file FILE` - JSON file with MCP servers array
- `--builtin-tools-file FILE` - JSON file with built-in tools config
- `--client-actions-file FILE` - JSON file with client actions array
- `--api-tools-file FILE` - JSON file with custom HTTP API tools array
- `--max-call-duration MINS` - Max call duration in minutes (5-30)
- `--max-history-messages N` - Max messages before compaction (20-100)
- `--continuous-mode true|false` - Keep talking after short silences instead of waiting for the user each turn (tutors, narrators, guided experiences). User can still interrupt by speaking.
- `--continuous-mode-delay SECS` - Seconds to wait before auto-continuing in continuous mode (1.0-8.0, default 2.0)
- `--ai-agent-enabled true|false` - Enable AI Agent integration
- `--ai-agent-description TEXT` - Description of the developer's AI agent
- `--ai-agent-verbatim true|false` - Speak agent responses verbatim
- `--ai-agent-file FILE` - JSON file with AI Agent config
- `--outbound-enabled true|false` - Enable outbound calling (Paid subscribers only)
- `--outbound-greeting TEXT` - Greeting for outbound calls (use '' to clear)
- `--accept-outbound-tos` - Accept Outbound Calling Terms of Use (required when enabling outbound)
- `--merge` - Deep-merge file contents with current config instead of replacing (for dict-based configs)

### Listener-mode options (only meaningful for `--style Listener` agents)

- `--coachee-description TEXT` - Names the person you're coaching. When set, the agent only generates a coaching card when *someone else* is speaking, and tailors each card as guidance for them. Example: `"the CFO during earnings Q&A"`.
- `--coaching-debounce SECS` - Minimum seconds between coaching cards (0-60). Higher = fewer, more spaced-out suggestions.
- `--coaching-context-turns N` - Recent turns of conversation the agent considers per coaching card (0-50).
- `--coaching-job-timeout SECS` - Max seconds to wait for each coaching card before giving up (5-120).
- `--coaching-gate true|false` - When On (default), coaching only appears when the conversation matches the trigger conditions in your prompt. When Off, every spoken turn produces a coaching card.
- `--speaker-map true|false` - When On (default), the agent identifies who's speaking and emits `speaker_map_update` events. When Off, your app only sees raw labels like `S0`, `S1`.
- `--speaker-map-interval SECS` - How often to re-check speaker identities (5-300).

Examples:

```bash
# Change agent style
vb config set --style Focused

# Enable debug mode
vb config set --debug-mode true

# Update multiple settings
vb config set --name "My Agent" --hold-enabled true --hangup-enabled true

# Set MCP servers from file
vb config set --mcp-servers-file mcp_servers.json

# Set session limits
vb config set --max-call-duration 15  # 15 minute max call duration
vb config set --max-history-messages 50  # Keep 50 messages before compaction

# Continuous speech ("keep talking" mode) — agent continues on its own after a
# short silence instead of waiting each turn. Great for tutors and narrators.
vb config set --continuous-mode true
vb config set --continuous-mode true --continuous-mode-delay 3  # wait 3s before continuing
vb config set --continuous-mode false  # back to normal turn-based behavior

# Set client actions from file
vb config set --client-actions-file client_actions.json

# Set custom HTTP API tools from file
vb config set --api-tools-file api_tools.json

# Enable AI Agent integration
vb config set --ai-agent-enabled true --ai-agent-description "Customer support agent for order tracking"

# Set AI Agent config from file
vb config set --ai-agent-file ai_agent.json

# Disable AI Agent integration
vb config set --ai-agent-enabled false

# Enable outbound calling (requires ToS acceptance)
vb config set --outbound-enabled true --accept-outbound-tos

# Set outbound greeting
vb config set --outbound-greeting "Hi, this is a call from our team."

# Disable outbound calling
vb config set --outbound-enabled false

# Tune a Listener-mode agent
vb config set --coachee-description "the candidate being interviewed" \
              --coaching-debounce 20

# Turn off the coaching gate so every spoken turn produces a card
vb config set --coaching-gate false

# Skip speaker identity inference entirely (app sees only S0/S1 labels)
vb config set --speaker-map false
```

### Edit full configuration

Opens the full configuration in $EDITOR as JSON:

```bash
vb config edit
```

This allows editing all settings at once. Save and close the editor to apply changes.

## Based on $ARGUMENTS

Determine user intent from $ARGUMENTS:
- "options" or "valid" -> show valid options with `vb config options`
- "options <name>" -> show options for specific setting
- "show" or empty -> show all settings with `vb config show`
- "show --json" or "json" -> show as JSON
- "get <section>" or "export" -> export config section with `vb config get <section>`
- "set" with options -> update specific settings
- "set" with "--merge" -> deep-merge file with current settings
- "edit" -> open full config in editor
- Contains setting names -> first run `vb config options <setting>` to show valid values, then use `vb config set`

**Best Practice**: When user wants to change a setting, ALWAYS run `vb config options <setting>` first to show them valid values before making changes. For roundtrip edits, use `vb config get <section>` to export current values first.

## Agent Styles

| Style | Description |
|-------|-------------|
| **Chatty** | Best for snappy, low-latency conversations. Ideal when most context fits in the system prompt and you only need 1-2 tools. |
| **Focused** | Best for information-heavy conversations like interviews, data collection, or surveys. More thorough responses. |
| **Gemini** | Powered by Google Gemini Live API. Great for natural, flowing conversations with multimodal capabilities. |
| **Ultravox** | Powered by Ultravox Realtime API. Optimized for voice-first interactions with native audio understanding. |
| **Listener** | Passive observer — never speaks. Joins a session, transcribes multi-speaker audio with diarization, runs your `custom_prompt` as a coaching policy, and streams `live_transcript` / `coaching_suggestion` / `speaker_map_update` actions to your app's data channel. Web-only (no phone). After creating one, run `vb docs` to see the action payload schemas. |

## JSON File Formats

### MCP Servers (mcp_servers.json)

```json
[
  {
    "url": "https://actions.zapier.com/mcp/...",
    "name": "Zapier",
    "tools": []
  }
]
```

### Model Settings (model_settings.json)

For Focused style:

```json
{
  "stt": {
    "model": "assemblyai:universal-streaming",
    "language": "en",
    "eot_threshold": 0.5
  },
  "tts": {
    "model": "eleven_multilingual_v2",
    "voice_id": "cgSgspJ2msm6clMCkdW9"
  },
  "session": {
    "max_call_duration_minutes": 30,
    "max_history_messages": 100,
    "continuous_mode": "false",
    "continuous_mode_delay": 2.0
  }
}
```

**Continuous speech** (`session.continuous_mode`): when `"true"`, the agent keeps talking on its own after a short silence (`continuous_mode_delay` seconds) instead of waiting for the user each turn — useful for tutors, narrators, and guided experiences. The user can still interrupt at any time by speaking. Available on all concierge styles (Chatty/Focused, Gemini, Ultravox). Defaults to `"false"` (normal turn-based behavior).

**Language options:**
- Preset: `en`, `multi` (auto-detect), `es`, `fr`, `de`, `pt`, `it`, `nl`, `ja`, `ko`, `zh`, `hi`, `ru`, `ar`, `pl`, `tr`, `vi`, `th`, `id`, `sv`, `da`, `fi`, `no`, `uk`, `cs`, `el`, `he`, `ro`, `hu`, `ms`, `bg`, `sk`, `hr`, `ca`, `ta`
- Custom: Use `language_source: "custom"` with `custom_language: "<BCP-47 code>"` (e.g., `en-US`, `pt-BR`, `zh-TW`)

For custom language code:

```json
{
  "stt": {
    "model": "deepgram:nova-3",
    "language_source": "custom",
    "custom_language": "pt-BR",
    "eot_threshold": 0.5
  }
}
```

### Built-in Tools (builtin_tools.json)

```json
{
  "lever": {
    "enabled": true,
    "api_key": "...",
    "posting_id": "..."
  },
  "client_actions": {
    "enabled": true,
    "actions": [
      {"name": "show_product", "description": "Display a product card", "direction": "agent_to_app"},
      {"name": "practice_result", "description": "User completed practice", "direction": "app_to_agent", "behavior": "notify"},
      {"name": "word_data", "description": "New word data from app", "direction": "app_to_agent", "behavior": "respond"}
    ]
  }
}
```

**Behavior field** (for `app_to_agent` actions only):
- `respond` (default): Agent generates a reply when this event arrives
- `notify`: Event is silently added to conversation context — agent sees it on next turn but does not reply immediately

### Client Actions (client_actions.json)

```json
[
  {"name": "show_product", "description": "Display a product card", "direction": "agent_to_app"},
  {"name": "user_clicked_buy", "description": "User clicked buy", "direction": "app_to_agent", "behavior": "respond"},
  {"name": "practice_result", "description": "Practice completed", "direction": "app_to_agent", "behavior": "notify"}
]
```

### Custom HTTP API Tools (api_tools.json)

```json
[
  {
    "id": "1",
    "name": "get_weather",
    "description": "Get current weather for a city",
    "method": "GET",
    "url": "https://api.weather.com/v1/current",
    "auth": {"type": "bearer", "credentials": {"token": "sk-xxx"}},
    "parameters": [
      {"name": "city", "type": "string", "description": "City name", "required": true, "location": "query"}
    ],
    "timeout": 30,
    "max_retries": 2,
    "enabled": true
  }
]
```

**API tool fields:**
- `id` (string): Unique identifier
- `name` (string): Tool name (letters, numbers, underscores only)
- `description` (string): What the tool does (max 500 chars)
- `method`: GET, POST, PUT, DELETE, or PATCH
- `url` (string): HTTPS URL
- `auth`: Optional — `bearer`, `basic`, `header`, `query`, or `none`
- `parameters`: Optional — array of `{name, type, description, required, location}`
- `timeout` (int): Seconds (1-300, default 30)
- `max_retries` (int): Retries on failure (0-5, default 2)
- `enabled` (bool): Whether tool is active (default true)
- Maximum 20 tools per agent

### AI Agent Config (ai_agent.json)

```json
{
  "enabled": true,
  "description": "Customer support agent for Acme Corp",
  "verbatim": false
}
```

**Fields:**
- `enabled` (boolean): Whether AI Agent integration is active
- `description` (string): What the developer's agent does (max 2000 chars). Helps the voice agent know when to delegate questions.
- `verbatim` (boolean): If true, voice agent speaks responses exactly as received; if false (default), adapts for natural voice delivery
