---
description: "Delegate a React Native / Expo task to the react-native-dev subagent"
argument-hint: "<task>"
---

Delegate this to the `react-native-dev` subagent: $ARGUMENTS

Rules:
1. Do not do the work yourself. The subagent has a fresh context: put the full task,
   the relevant file paths, and any approved plan in its prompt.
2. If the task is ambiguous, ask me before delegating (R4).
3. Relay its report: files changed, test command and result, what it could not run.
