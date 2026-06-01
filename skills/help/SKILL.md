---
name: help
description: Show help for Vocal Bridge Claude Code plugin commands
---

# Vocal Bridge Plugin Commands

| Command | Description |
|---------|-------------|
| `/vocal-bridge:login [api_key]` | Authenticate with API key |
| `/vocal-bridge:status` | Check authentication status |
| `/vocal-bridge:agent` | Show agent information |
| `/vocal-bridge:create` | Create and deploy a new agent (paid subscribers only) |
| `/vocal-bridge:delete [agent_id]` | Delete an agent permanently |
| `/vocal-bridge:logs [session_id]` | View call logs |
| `/vocal-bridge:download <session_id>` | Download call recording |
| `/vocal-bridge:stats` | Show call statistics |
| `/vocal-bridge:prompt [show\|set]` | Manage system prompt |
| `/vocal-bridge:config [show\|get\|set\|edit\|options]` | Manage all agent settings |
| `/vocal-bridge:call <phone_number>` | Place an outbound call, optionally with `--var KEY=VALUE` dynamic variables (Pilot only) |
| `/vocal-bridge:call-action <call_id> <action>` | Inject a configured `app_to_agent` event into a live outbound call |
| `/vocal-bridge:session-action <session_id> <action>` | Inject into a live call session (inbound, outbound, or web) |
| `/vocal-bridge:eval <session_id>` | Evaluate a call recording with a multimodal LLM (Pilot only, 100/day) |
| `/vocal-bridge:debug` | Stream debug events |
| `/vocal-bridge:setup` | Install CLI if needed |

## Getting Started

1. Get your API key from your agent detail page at https://vocalbridgeai.com
2. Run `/vocal-bridge:login vb_your_api_key` to authenticate
3. Use `/vocal-bridge:agent` to verify connection

## Examples

```
# Login with your API key
/vocal-bridge:login vb_abc123xyz

# Check connection status
/vocal-bridge:status

# View recent call logs
/vocal-bridge:logs

# View last 50 failed calls
/vocal-bridge:logs -n 50 --status failed

# View specific call transcript
/vocal-bridge:logs 550e8400-e29b-41d4-a716-446655440000

# Download call recording
/vocal-bridge:download 550e8400-e29b-41d4-a716-446655440000

# Create a new agent (paid subscribers only)
/vocal-bridge:create --name "My Agent" --style Chatty --prompt "You are helpful."

# Delete an agent (requires confirmation)
/vocal-bridge:delete

# Show call statistics
/vocal-bridge:stats

# View current prompt
/vocal-bridge:prompt show

# View all agent settings
/vocal-bridge:config show

# Export a config section as JSON
/vocal-bridge:config get model-settings
/vocal-bridge:config get client-actions

# Show valid options for a setting (ALWAYS do this before changing settings)
/vocal-bridge:config options voice
/vocal-bridge:config options "TTS Model"

# Update agent settings
/vocal-bridge:config set --debug-mode true

# Partial update with merge (only changes specified fields)
/vocal-bridge:config set --model-settings-file update.json --merge

# Set session limits
/vocal-bridge:config set --max-call-duration 15
/vocal-bridge:config set --max-history-messages 50

# Enable outbound calling
/vocal-bridge:config set --outbound-enabled true --accept-outbound-tos

# Place an outbound call
/vocal-bridge:call +14155551234

# Place an outbound call with dynamic variables (Vapi/Retell-style {{var}} substitution)
/vocal-bridge:call +14155551234 --var customer_name=Jane --var appointment_time=3pm

# Inject a configured app_to_agent event into a live outbound call
/vocal-bridge:call-action <call_id> user_clicked_buy --payload '{"product_id":"ABC"}'

# Inject into any live session (inbound, outbound, or web)
/vocal-bridge:session-action <session_id> appointment_confirmed

# Configure agent_to_app webhook (HMAC-signed)
/vocal-bridge:config set --client-actions-webhook-url https://your-backend.example.com/hook
/vocal-bridge:config set --regenerate-client-actions-webhook-secret  # rotate

# Evaluate a call recording (Pilot only, 100/day)
/vocal-bridge:eval 550e8400-e29b-41d4-a716-446655440000
/vocal-bridge:eval <session_id> --objective "Schedule an interview" --scenario "User is busy"

# Edit full config in editor
/vocal-bridge:config edit

# Stream debug events
/vocal-bridge:debug
```

## CLI Installation

The plugin automatically installs the CLI when needed. You can also install manually:

```bash
pip install vocal-bridge
```

Or download the standalone script:

```bash
curl -fsSL https://vocalbridgeai.com/cli/vb.py -o vb && chmod +x vb
```

## More Information

- Documentation: https://vocalbridgeai.com/docs/developer-guide
- Dashboard: https://vocalbridgeai.com
- GitHub: https://github.com/vocalbridgeai/vocal-bridge-claude-plugin
