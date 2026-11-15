---
description: "Delegate a Blender Python (bpy) task to the blender-dev subagent"
argument-hint: "<task>"
---

Delegate this to the `blender-dev` subagent: $ARGUMENTS

Rules:
1. Do not do the work yourself. The subagent has a fresh context: put the full task,
   the relevant file paths, the target Blender version if known, and any approved
   plan in its prompt.
2. If the task is ambiguous, ask me before delegating (R4).
3. Relay its report: files changed, test commands and results, what it could not run.
