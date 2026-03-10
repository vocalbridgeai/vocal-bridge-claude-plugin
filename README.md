# Vocal Bridge Plugin for Claude Code

Manage your Vocal Bridge voice agents directly from Claude Code. View call logs, update prompts, stream debug events, and more.

## Installation

### From Marketplace (Recommended)

```
/plugin marketplace add vocalbridgeai/vocal-bridge-marketplace
/plugin install vocal-bridge@vocal-bridge
```

### Manual Installation

```bash
# Clone the plugin
git clone https://github.com/vocalbridgeai/vocal-bridge-claude-plugin.git

# Load in Claude Code
claude --plugin-dir ./vocal-bridge-claude-plugin
```

## Getting Started

1. **Get your API key** from your agent detail page or create an account key from the dashboard (paid subscribers)

2. **Login** to connect Claude Code to your agent:
   ```
   /vocal-bridge:login vb_your_api_key_here
   ```

3. **For account keys**, select an agent to work with:
   ```
   vb agent use
   ```

4. **Verify connection**:
   ```
   /vocal-bridge:status
   ```

## Commands

| Command | Description |
|---------|-------------|
| `/vocal-bridge:login [api_key]` | Authenticate with your API key |
| `/vocal-bridge:status` | Check authentication status |
| `/vocal-bridge:agent` | Show agent information |
| `/vocal-bridge:logs [session_id]` | View call logs or session details |
| `/vocal-bridge:download <session_id>` | Download call recording audio |
| `/vocal-bridge:stats` | Show call statistics |
| `/vocal-bridge:prompt [show\|set]` | Manage system prompt |
| `/vocal-bridge:config [show\|set\|edit\|options]` | Manage all agent settings |
| `/vocal-bridge:create` | Create and deploy a new agent (paid subscribers only) |
| `/vocal-bridge:debug` | Stream real-time debug events |
| `/vocal-bridge:setup` | Install CLI if needed |
| `/vocal-bridge:help` | Show all commands |

## Examples

### View Call Logs

```
# List recent calls
/vocal-bridge:logs

# Show last 50 failed calls
/vocal-bridge:logs -n 50 --status failed

# View specific call transcript
/vocal-bridge:logs 550e8400-e29b-41d4-a716-446655440000
```

### Download Call Recordings

```
# Download recording for a session
/vocal-bridge:download 550e8400-e29b-41d4-a716-446655440000

# Download with custom filename
/vocal-bridge:download 550e8400-e29b-41d4-a716-446655440000 -o call.ogg
```

### Manage Prompts

```
# View current prompt
/vocal-bridge:prompt show

# Update prompt (will guide you through the process)
/vocal-bridge:prompt set
```

### Manage Agent Configuration

```
# View all agent settings
/vocal-bridge:config show

# View settings as JSON
/vocal-bridge:config show --json

# Discover valid options for a setting (IMPORTANT: do this before changing settings)
/vocal-bridge:config options voice          # by setting name
/vocal-bridge:config options "TTS Model"    # by label (same as UI)
/vocal-bridge:config options audio          # all settings in a category

# Update specific settings
/vocal-bridge:config set --style Focused
/vocal-bridge:config set --debug-mode true
/vocal-bridge:config set --hold-enabled true --hangup-enabled true

# AI Agent integration
/vocal-bridge:config set --ai-agent-enabled true --ai-agent-description "Customer support agent"
/vocal-bridge:config set --ai-agent-file ai_agent.json

# Edit full configuration in $EDITOR
/vocal-bridge:config edit
```

### Create New Agent (Paid Subscribers)

Create and deploy a new voice agent directly from Claude Code. Requires an active paid subscription. Maximum 50 agents per account.

```
# Create a simple chatty agent
/vocal-bridge:create --name "My Assistant" --style Chatty --prompt "You are a helpful assistant."

# Create with a greeting and web-only deployment
/vocal-bridge:create --name "Web Agent" --style Focused --prompt "You help users." --greeting "Hello!" --deploy-targets web

# Create from a prompt file
/vocal-bridge:create --name "Custom Agent" --style Chatty --prompt-file prompt.txt
```

### Debug Live Calls

```
# Stream debug events (requires debug mode enabled)
/vocal-bridge:debug
```

## Prerequisites

The plugin automatically installs the Vocal Bridge CLI when needed. You can also install it manually:

```bash
pip install vocal-bridge
```

## Requirements

- Claude Code 1.0.33 or later
- Python 3.9+ (for CLI)
- A Vocal Bridge account with a deployed agent

## Links

- [Vocal Bridge Dashboard](https://vocalbridgeai.com)
- [Documentation](https://vocalbridgeai.com/docs/developer-guide)
- [CLI on PyPI](https://pypi.org/project/vocal-bridge/)

## License

Apache-2.0
