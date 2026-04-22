---
name: delete
description: Delete a Vocal Bridge voice agent permanently. Releases the phone number and removes the agent.
allowed-tools: Bash
---

Delete a voice agent permanently, releasing its phone number and cleaning up all resources.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

Then run:

```bash
vb agent delete
```

This will:
1. Show the agent details (name, ID, phone number)
2. Ask you to type the agent name to confirm deletion
3. Delete the dispatch rule and release the phone number
4. Remove the agent record

To delete a specific agent by ID:

```bash
vb agent delete <agent_id>
```

To skip the confirmation prompt (use with caution):

```bash
vb agent delete --force
```

**Warning:** This action is permanent and cannot be undone. The phone number will be released and may be reassigned.
