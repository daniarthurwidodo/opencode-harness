---
description: Browser automation through the Playwright CLI
agent: build
---

Do this browser task with the Playwright CLI: $ARGUMENTS

Rules:
1. Run `npx -y playwright <command>` directly. Do not add playwright to any project unless the user asks.
2. Save artifacts under `/tmp/opencode/playwright/` and report the full path.
3. Pick the command that fits:
   - Screenshot: `npx -y playwright screenshot --browser chromium --full-page --wait-for-timeout 2000 <url> /tmp/opencode/playwright/<name>.png`
   - PDF: `npx -y playwright pdf <url> /tmp/opencode/playwright/<name>.pdf`
   - Test run: `npx -y playwright test <path>` (needs playwright in that project)
   - Codegen or open: `npx -y playwright codegen <url>`, `npx -y playwright open <url>` (needs a display; tell the user if it fails headless)
4. Multi-step interaction (click, fill, login, scrape text): use the playwright MCP tools in this session instead of the CLI. Do not write a script that imports playwright, because npx does not expose the module to `node`.
5. If a browser binary is missing, run `npx -y playwright install chromium` once, then retry.
6. Verify the artifact exists (`ls -la <path>`) before you report success. Report the command you ran and any failure text verbatim.
