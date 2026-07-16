---
name: mcp
description: Test the agent's background AI and MCP/API tools with a query, without placing a call.
allowed-tools: Bash
---

Test the agent's background AI system — including any MCP servers, HTTP API tools, and connectors it has configured — by sending a query directly, without placing a call.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

Then run:

```bash
# Run a background query and print the result
vb mcp test "What's on my calendar tomorrow?"

# Adjust the timeout (seconds, 5-120) and get JSON
vb mcp test "Look up order 12345" --timeout 60 --json
```

Use this to verify that MCP servers, API tools, and connectors respond as expected before testing on a live call.
