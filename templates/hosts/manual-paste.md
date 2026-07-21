<!--
graphkit host adapter: manual paste — the fallback host for either node when no
goal/cron runtime is available. Replace {{RUN_DIR}}.
-->

# Host adapter — manual paste (either node)

No goal or cron runtime? Any fresh chat is a host. Open a new session and paste:

```text
Read and follow {{RUN_DIR}}/executor.md      # executor node
# — or —
Read and follow {{RUN_DIR}}/supervisor.md    # supervisor node (must be its OWN session)
```

Then drive rounds by re-sending "continue" (executor) or re-opening a session each
interval (supervisor). State lives in the ledger, so stopping and resuming loses
nothing — but this is turn-based babysitting; prefer a goal host (`grok-goal.md`) for
the executor and a cron host (`claude-cron.md`) for the supervisor when available.

Rule that still holds here: the executor and supervisor never share one session.
