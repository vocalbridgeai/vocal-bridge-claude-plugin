---
name: docs
description: Get developer documentation for integrating your Vocal Bridge agent into applications.
allowed-tools: Bash
---

Get developer integration documentation for the Vocal Bridge voice agent.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

Then run:

```bash
vb docs $ARGUMENTS
```

This returns comprehensive developer documentation including:
- Agent configuration and capabilities
- MCP server integration
- Client actions (bidirectional communication)
- Custom HTTP API tools
- AI Agent integration
- Connection heartbeat and live transcript
- API integration guide with token generation
- Implementation examples (JavaScript, React, Flutter)
- CLI and Claude Code plugin reference
- Security best practices

The output is markdown formatted. If an agent is selected, the docs are customized with agent-specific configuration, tools, and actions. Without an agent selected, generic capability docs are returned.

Use `--json` flag for JSON output.

## How to Use the Output

The documentation returned by this command is designed to be used as context for building integrations. You can:

1. **Read it directly** to understand how to integrate the voice agent
2. **Use it as reference** when writing integration code
3. **Copy code examples** for JavaScript, React, or Flutter implementations
