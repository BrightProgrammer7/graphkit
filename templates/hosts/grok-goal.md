<!--
graphkit host adapter: Grok /goal — thin launcher for the EXECUTOR node.
Replace {{RUN_DIR}} with the run's `.graphkit/<slug>/` path. Paste the fenced block
into Grok's /goal input. This REPLACES pasting the whole executor.md into /goal:
the goal holds only a pointer; the authority stays in the files, so a restart is the
same one-liner and nothing goes stale.
Only the executor runs here. The supervisor is always a different host (see
claude-cron.md) — never a second goal inside this one.
-->

# Host adapter — Grok `/goal` (executor node)

Paste this into Grok `/goal`. It launches the executor and keeps it running across
turns without stopping to ask between rounds — a goal, not a chat.

```text
/goal You are the executor node of a graphkit run. Read and obey ONLY these files;
do not fold their contents into this goal text — re-read them each round.

  instructions : {{RUN_DIR}}/executor.md
  scoreboard   : {{RUN_DIR}}/ledger.md      (single source of truth)
  corrections  : {{RUN_DIR}}/directives.md  (read-only to you)
  environment  : {{RUN_DIR}}/ops.md

Run continuously:
- Each round = the single smallest unclosed ledger item → verify same round →
  update the ledger. Then start the next round immediately.
- Do NOT stop to ask "shall I continue?" between rounds. Keep going until a stop
  condition in executor.md fires (milestone exit-ready / all items blocked /
  two rounds no change / red line).
- If this session ends, a new /goal with this same block resumes from the ledger —
  no state is lost, because the ledger was updated each round.

Goal progress API (only if the host exposes it, e.g. update_goal):
- ledger advanced          → message = "Round N · <item> · gate <green|red>"
- owner-blocked / red line  → blocked_reason = <one line>
- promotion request written OR ledger run status = `exit-ready` → completed=true
- NEVER completed=true while gates are green but milestone items remain open.
  The goal's done-bar mirrors the ledger; the ledger wins every conflict.

Boundaries:
- You are ONLY the executor. Do not edit directives.md. Do not act as supervisor.
- Subagents may explore / test / inventory; only you mark items verified in the ledger.
```

## Why thin, not full paste

- One source of truth for the executor's instructions (the file) — the goal text and
  `executor.md` can't drift apart mid-run.
- Cheap restart: after a dead session, the same one-liner resumes; state lives in the ledger.
- Smaller goal prompt; the long rules stay in the file the node re-reads.
