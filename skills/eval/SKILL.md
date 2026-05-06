---
name: eval
description: Evaluate a call recording with a multimodal LLM. Sends the audio, the agent's prompt and configuration, the structured session log (transcript + tool calls + client actions), and the raw session report for a qualitative QA assessment. Pilot subscribers only, 100 evals per day.
allowed-tools: Bash
---

Evaluate a Vocal Bridge call recording with a multimodal LLM. Returns a structured score, verdict, and concrete suggestions for improving the agent's prompt.

First ensure CLI is installed and up to date:

```bash
pip install --upgrade vocal-bridge
```

## Usage

```bash
vb eval <session_id> [--objective "..."] [--scenario "..."] [--objective-file FILE] [--scenario-file FILE] [--json]
```

The evaluator model is fixed by the platform.

## Examples

```bash
# Basic eval against the agent's own system prompt
vb eval 550e8400-e29b-41d4-a716-446655440000

# With a specific objective
vb eval <session_id> --objective "Schedule an interview for next Tuesday"

# With both an objective and an expected scenario
vb eval <session_id> \
  --objective "Confirm the candidate's availability" \
  --scenario "The user is busy and tries to reschedule twice"

# Read long objective/scenario from files
vb eval <session_id> --objective-file objective.txt --scenario-file scenario.txt

# Raw JSON output (for piping into another tool)
vb eval <session_id> --json
```

## What gets sent to the evaluator

- Full audio recording of the call (inline, up to ~18 MB)
- Agent's current system prompt (from `vb config show`)
- Caller-supplied `--objective` and `--scenario` (optional)
- Structured transcript with the agent's tool calls
- Client action events log (heartbeats and custom bidirectional events between the agent and the client app)
- Raw session report (for troubleshooting context)

The evaluator uses the audio as the primary source of truth for tone, interruptions, and timing, and uses the structured logs to verify what tools/actions actually fired.

## Output

```
Call Evaluation
----------------------------------------
  Session:      550e8400...
  Score:        7/10
  Verdict:      partial

Summary:
  The agent answered the user's questions accurately but missed
  the scheduling objective when the user asked to reschedule.

What worked:
  + Greeted the caller naturally
  + Recovered cleanly from a mid-sentence interruption

What didn't:
  - Did not call schedule_meeting tool when the user gave a date
  - Tone became impatient on the second reschedule attempt

Suggested prompt improvements:
  Add an explicit instruction to call schedule_meeting whenever
  the user proposes any time, including reschedules.
```

## Restrictions

- **Pilot subscription required** — `403` if not subscribed
- **100 evals/day per user** across all your agents — `429` when exceeded
- **18 MB inline audio cap** per recording — extremely long calls will be rejected
- The session must already have a recording (`vb logs <session_id>` will tell you)

## Based on $ARGUMENTS

- Contains a UUID -> use it as the session_id
- Contains `--objective "..."` -> pass through
- Contains `--scenario "..."` -> pass through
- Contains `--objective-file` or `--scenario-file` -> pass through
- Contains `--json` -> output raw JSON
- Empty -> show usage instructions and a hint to use `/vocal-bridge:logs` to find a session_id
