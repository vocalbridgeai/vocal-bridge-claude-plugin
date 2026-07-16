---
name: connectors
description: List native OAuth connectors and get a link to connect one to the agent.
allowed-tools: Bash
---

Native connectors let the agent use third-party services (e.g. Google Calendar) via OAuth.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

List connectors and their connected / enabled-on-agent status:

```bash
vb connectors list
vb connectors list --json
```

Get a link to connect a connector (OAuth runs in the browser and auto-enables it on the agent):

```bash
vb connectors connect google_calendar
```

Per-agent connector settings can be exported and re-applied as JSON:

```bash
vb config get connectors > connectors.json
# edit connectors.json...
vb config set --connectors-file connectors.json --merge
```
