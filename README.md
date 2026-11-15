# OpenCode Harness

Reproducible Linux setup for the global OpenCode configuration and automation harness. It contains no API credentials, browser profiles, Unity authentication tokens, or saved memories.

## Included

| Component | Version |
| --- | --- |
| OpenCode | 2.3.0 |
| Ponytail | 5.3.3 |
| Caveman | `@mumme-it/opencode-caveman@1` (1.0.3) |
| Subagents Sidebar | `opencode-plugin-subagents-sidebar` (0.2.1) |
| Playwright MCP | 0.0.84 |
| Playwright CLI | 0.1.23 |
| Playwright CLI (npx, browser automation) | 1.65.1 |
| Playwright Chromium build | `chromium-1255` + `chromium_headless_shell-1255` |
| Knowledge Graph Memory MCP | 2.1.0 |
| Cavemem | 0.2.1 |
| Geckodriver | 0.37.1 |
| MCP Unity checkout | `d572dc88f1b982a0fdfc09cd14cfdfd760b658e8` |

The configuration includes global engineering rules, memory usage rules, safety permissions, Chromium browser automation through Playwright, terse-reply compression, and model warming **disabled**.

## 1. Prerequisites

- Linux; the Geckodriver commands below target `x86_64`.
- Git, curl, tar, and npm.
- Node.js 24 or newer, required by the current memory server.
- OpenCode 2.2.6: use the [official installation documentation](https://opencode.ai/docs/).
- Firefox if you want to use Geckodriver. Playwright downloads its own Firefox build separately.

Make sure your user-local binaries are on `PATH`:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Add that line to your shell startup file if it is not already configured.

## 2. Install the global configuration

Back up any existing `~/.config/opencode` files before overwriting them. Then, from this repository:

```bash
mkdir -p "$HOME/.config/opencode" "$HOME/.local/share/opencode"
cp opencode.json cli.json AGENTS.md rules.md "$HOME/.config/opencode/"
cp -r agents commands "$HOME/.config/opencode/"
```

This installs:

- `opencode.json`: MCP servers, server plugins, and permissions.
- `cli.json`: terminal-UI plugins, loaded by the TUI process only.
- `agents/`: one primary agent (`orchestrator`) and thirteen subagents, including `react-native-dev`, `game-dev`, `blender-dev`, `qa-engineer` and `sdet`.
- `commands/`: `/pipeline <task>`, `/browser <task>`, and `/rn`, `/game`, `/blender`, `/qa`, `/sdet <task>`, which hand a task to the matching subagent.
- `AGENTS.md`: OpenCode-specific memory rules.
- `rules.md`: the global engineering rules previously loaded from Claude.

## 3. Install Playwright CLI, Cavemem, and Playwright Chromium

```bash
npm install --global --prefix "$HOME/.local" \
  @playwright/cli@0.1.23 \
  cavemem@0.2.1
```

The MCP server pins its own Playwright build, so install the browser with that
build, not with the global CLI:

```bash
npx -y @playwright/mcp@0.0.84 --help >/dev/null   # download the MCP package once

node "$HOME/.npm/_npx"/*/node_modules/playwright-core/cli.js install chromium
```

Verify:

```bash
playwright-cli --version
npx -y playwright --version
ls "$HOME/.cache/ms-playwright"   # chromium-* and chromium_headless_shell-* present
```

The `/browser` command uses `npx -y playwright` and needs the browser revision
that matches that package. Install it once if a run reports a missing executable:

```bash
npx -y playwright install chromium
```

## 4. Install Geckodriver

Geckodriver is for Selenium/WebDriver. Playwright does not use it.

```bash
curl --fail --location \
  https://github.com/mozilla/geckodriver/releases/download/v0.37.1/geckodriver-v0.37.1-linux64.tar.gz \
  --output /tmp/geckodriver-v0.37.1-linux64.tar.gz

printf '%s  %s\n' \
  'e815130ea95983e162ae91843b48d3a3ce991735635fce83a647afde21e09f7e' \
  '/tmp/geckodriver-v0.37.1-linux64.tar.gz' | sha256sum --check

mkdir -p "$HOME/.local/bin"
tar -xzf /tmp/geckodriver-v0.37.1-linux64.tar.gz \
  -C "$HOME/.local/bin" geckodriver

geckodriver --version
```

For another CPU architecture, download the matching release from Mozilla and verify its published digest instead.

## 5. Optional: configure MCP Unity

Skip this section and remove the `unity` server block from the copied `opencode.json` if you do not use Unity.

Clone and build the Node server at the recorded revision:

```bash
git clone https://github.com/CoderGamester/mcp-unity.git \
  "$HOME/.local/share/mcp-unity"

git -C "$HOME/.local/share/mcp-unity" checkout \
  d572dc88f1b982a0fdfc09cd14cfdfd760b658e8

npm --prefix "$HOME/.local/share/mcp-unity/Server~" ci
npm --prefix "$HOME/.local/share/mcp-unity/Server~" run build
```

In Unity, open **Window > Package Manager**, choose **Add package from git URL**, and add:

```text
https://github.com/CoderGamester/mcp-unity.git
```

Open the Unity project once so it creates `ProjectSettings/McpUnitySettings.json`. Then either:

1. Set `MCP_UNITY_SETTINGS_PATH` in the environment of the OpenCode server to that file's absolute path, or
2. Replace `{env:MCP_UNITY_SETTINGS_PATH}` in the copied `opencode.json` with the absolute path.

Do not commit Unity's `Library/McpUnity/bridge-token` or copy it into this repository.

## 6. Connect the model provider

Provider credentials are intentionally not included. In OpenCode:

```text
/connect
/models
```

Select the provider and model you want on the new PC.

## 7. Verify the harness

```bash
opencode mcp list
opencode plugin list
```

Expected MCP servers:

```text
cavemem
graph
playwright
unity
```

Expected plugins:

```text
harness (local)  @dietrichgebert/ponytail  @mumme-it/opencode-caveman
opencode-plugin-subagents-sidebar (TUI, from cli.json)
```

`harness` is the local plugin at `~/.config/opencode/plugins/harness`; it is not
published and must be copied separately. If it is missing, `/approve`, the shell
block, and the per-agent prompt text are inactive.

`opencode plugin add <package>` writes server plugins to `opencode.json` and
terminal-UI plugins to `cli.json`; the TUI must be restarted to load the latter.

Only OpenCode V2 plugins load. Packages built against the V1 API
(`@opencode-ai/plugin`) fail at startup with "Plugin must export a default
definition with an id and an effect or setup function". That is why the caveman
plugin is `@mumme-it/opencode-caveman`, which is V2-only; `opencode-caveman` and
`caveman-opencode-plugin` are V1 and do not work here.

Caveman compresses replies in every session at `full` by default. Change or disable
it with `/caveman lite`, `/caveman ultra`, or `/caveman off`. It is style guidance,
not output rewriting, and it overrides nothing about tool permissions.

Unity can require its editor and settings path before it becomes usable. The graph starts empty at:

```text
~/.local/share/opencode/knowledge-graph.jsonl
```

No saved graph or Cavemem data is transferred by this repository.

## Safety behavior

The copied configuration:

- Asks before shell commands and external-directory access.
- Allows routine read-only Git and search commands.
- Allows `npx -y playwright *` for the `/browser` command; every other shell command still asks.
- Denies `sudo` and `git push` by default.
- Leaves model warming disabled.

Review these rules before enabling broader automation on another machine.
