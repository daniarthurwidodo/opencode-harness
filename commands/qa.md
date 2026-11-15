---
description: Delegate a behavioural check to the qa-engineer subagent
agent: build
---

Delegate this to the `qa-engineer` subagent: $ARGUMENTS

Rules:
1. Do not test it yourself. The subagent has a fresh context: put in its prompt the
   feature or fix under test, the acceptance criteria (the contents of `docs/prd.md`
   if it exists), how to reach the app, and the files changed.
2. If there are no acceptance criteria, say so in the prompt; it will list them as
   open questions instead of inventing any.
3. Relay its report: verdict, case table, bugs with repro steps, what was not run.
