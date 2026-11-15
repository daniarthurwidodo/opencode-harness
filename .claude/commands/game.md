---
description: "Delegate a Unity gameplay task to the game-dev subagent"
argument-hint: "<task>"
---

Delegate this to the `game-dev` subagent: $ARGUMENTS

Rules:
1. Do not do the work yourself. The subagent has a fresh context: put the full task,
   the relevant script paths, and any approved plan in its prompt.
2. If the task is ambiguous, ask me before delegating (R4).
3. Relay its report: files changed, MCP scene changes, test and compile results, what
   it could not run.
