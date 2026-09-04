# kev-slack-plugin

Slack status reactions for Cursor cloud agents.

## Why this exists

Cursor posts `Launched @someone's follow-up for the cloud agent!` when a Slack
thread follow-up is accepted. That acknowledgement carries no indication of
whether the agent is still working.

The first turn of a Slack-triggered automation already gets a working
indicator on the trigger message, swapped for a completion reaction when the
run finishes. Follow-up turns do not close that loop.

Putting the instruction in an automation's Agent Instructions does not fix it.
On a follow-up turn Cursor injects a reminder to treat the message as a new
request and not to re-execute the original automation instructions, and the
reaction steps read as exactly that. This plugin tests whether a rule — which
is not the automation's instructions — survives that reminder.

## Contents

| Rule | Purpose |
| --- | --- |
| `rules/slack-ack-status-reactions.mdc` | Mark the follow-up acknowledgement with `hourglass_flowing_sand`, then `white_check_mark` or `warning`. |
| `rules/plugin-load-probe.mdc` | Temporary. Prefixes replies with `ACK` to prove rules load at all. Delete after verifying. |

## Requirements

The agent needs a Slack MCP connection providing `slack_read_thread` and
`slack_add_reaction`. The stock Slack MCP has no remove-reaction tool, so the
hourglass remains next to the final marker.

## Testing

1. Install the plugin and send a Slack follow-up in a thread where a Cursor
   acknowledgement exists.
2. Check whether the reply starts with `ACK`. That answers whether plugin
   rules reach the agent, separately from whether the reaction rule is obeyed.
3. Check the acknowledgement message for the two reactions.

A blocked or never-started run cannot mark itself, so `warning` only appears
when the agent is alive enough to call the tool.
