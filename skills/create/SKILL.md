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
- `--style` - Agent style: Chatty, Focused, Gemini, or Ultravox
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
```

## Notes

- Requires an active paid subscription
- Maximum 50 total agents per account
- Agent will be deployed to web by default. Subscribe to Pilot to deploy on phone numbers
- After creation, create an API key in the dashboard to manage the new agent via CLI
