<!--
graphkit host adapter: Claude Code CronCreate — schedules the SUPERVISOR node.
Replace {{RUN_DIR}} and {{INTERVAL}}. Each tick is a NEW clean context: that is the
whole point. Never run the supervisor as a subagent inside the executor's session.
-->

# Host adapter — Claude Code cron (supervisor node)

The supervisor fires on a schedule, each tick a brand-new clean context reading only
durable state (ledger + `git status`). Schedule its body with `CronCreate`:

- Cron expression: off the hour/half-hour so parallel runs don't stampede — e.g.
  `7,37 * * * *` for a ~30-min interval.
- Prompt = the generated `{{RUN_DIR}}/supervisor.md` (verbatim; it points back at the
  ledger and directives).
- Give this node a **strong** model — cold-read drift-judging is the hardest call; it
  fires ~2×/hr so it stays cheap in aggregate.

Each tick: read state → progress check → checkpoint-commit clean gate-green work (if
authorized) → correct drift/method only by appending to `directives.md` → decide off
the owner-only list, escalate only owner-only items. It never writes the ledger.

No Claude Code / no cron? Start a fresh session every `{{INTERVAL}}` and paste
`supervisor.md` — any scheduler works; a clean context each tick is what matters.
