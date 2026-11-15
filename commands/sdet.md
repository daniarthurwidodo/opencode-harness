---
description: Delegate automated-test work to the sdet subagent
agent: build
---

Delegate this to the `sdet` subagent: $ARGUMENTS

Rules:
1. Do not write the tests yourself. The subagent has a fresh context: put the
   behaviour to prove, the paths of the code under test, and the test command in its
   prompt.
2. It cannot edit production code. If it reports a missing seam, show me before
   anyone changes the code.
3. Relay its report: tests added, commands and results (including the red run for a
   bug fix), what it could not run.
