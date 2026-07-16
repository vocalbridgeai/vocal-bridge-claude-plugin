---
name: post-processing
description: Run the agent's post-call processing against a sample transcript, without placing a call.
allowed-tools: Bash
---

Run the agent's configured post-call processing against a transcript to preview the result.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

Then run:

```bash
# Provide the transcript inline, from a file, or on stdin
vb post-processing test "User: I'd like to reschedule to Tuesday. Agent: Done."
vb post-processing test --file transcript.txt
cat transcript.txt | vb post-processing test

# JSON output
vb post-processing test --file transcript.txt --json
```

**Warning:** this runs the agent's LIVE post-call tools — MCP, HTTP API tool, and connector actions can make real changes (e.g. creating records). Use a disposable/test transcript.

Configure post-processing with `vb config set --post-processing-prompt`, `--post-processing-mcp-url`, and `--post-processing-model` (see the `config` skill).
