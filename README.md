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

1. **Get your API key** from your agent detail page at [vocalbridgeai.com](https://vocalbridgeai.com)

2. **Login** to connect Claude Code to your agent:
   ```
   /vocal-bridge:login vb_your_api_key_here
   ```

3. **Verify connection**:
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
