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

### Update individual settings

```bash
vb config set [OPTIONS]
```

Available options:
- `--name NAME` - Agent name
- `--style STYLE` - Agent style (Chatty, Focused, Gemini, Ultravox)
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
- `--ai-agent-enabled true|false` - Enable AI Agent integration
- `--ai-agent-description TEXT` - Description of the developer's AI agent
- `--ai-agent-verbatim true|false` - Speak agent responses verbatim
- `--ai-agent-file FILE` - JSON file with AI Agent config

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
- "set" with options -> update specific settings
- "edit" -> open full config in editor
- Contains setting names -> first run `vb config options <setting>` to show valid values, then use `vb config set`

**Best Practice**: When user wants to change a setting, ALWAYS run `vb config options <setting>` first to show them valid values before making changes.

## Agent Styles

| Style | Description |
|-------|-------------|
| **Chatty** | Best for snappy, low-latency conversations. Ideal when most context fits in the system prompt and you only need 1-2 tools. |
| **Focused** | Best for information-heavy conversations like interviews, data collection, or surveys. More thorough responses. |
| **Gemini** | Powered by Google Gemini Live API. Great for natural, flowing conversations with multimodal capabilities. |
| **Ultravox** | Powered by Ultravox Realtime API. Optimized for voice-first interactions with native audio understanding. |

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
    "max_history_messages": 100
  }
}
```

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
