# tuwezip-mcp

Windows x64 MCP server for [TuweZip](https://zip.tuwe.com) 0.3.8. An AI client starts `tuwezip-mcp.exe` on this computer and talks to it over stdio. The server packs ZIP, 7z, and tar.gz, and it seals or opens `.kas` / `.tuwe` payment archives.

This repository holds the client examples and the rights notice. The program is the Release asset `tuwezip-mcp-0.3.8-win-x64.zip`. Its SHA-256 is in [checksums.txt](checksums.txt). The desktop installer is a separate download and is not this file.

The program is licensed to install and run. See [NOTICE.zh.txt](NOTICE.zh.txt) and [NOTICE.en.txt](NOTICE.en.txt). This repository is not an open-source release of the program.

## Install

1. Download `tuwezip-mcp-0.3.8-win-x64.zip` from the [v0.3.8 release](https://github.com/lifeme2027/tuwezip-mcp/releases/tag/v0.3.8).
2. Check the SHA-256 against `checksums.txt`.
3. Unzip the whole archive to a folder, for example `C:\TuweZip\mcp`. `tuwezip-mcp.exe` and `kas_conn.dll` stay side by side.
4. Point the AI client at that exe. Set the working directory to the same folder.
5. Set `TUWE_AGENT_ALLOW_DIRS` to the folders the client may read and write. Use `;` between folders.
6. Set `BIDXIN_WALLET_PASSWORD` in the client that starts the process. Leave it empty until you do. An empty value does not unlock a wallet. Do not commit a password, a private key, or a `.kaswallet` file.

The zip is self-contained for Windows x64. macOS and Linux builds are not in this release. The executable is not code-signed, so Windows SmartScreen may ask before the first start.

## Tools

| Tool | Effect |
| --- | --- |
| `tuwe_pack` | Create a zip, 7z, or tar.gz. A compression password comes only from `TUWE_ARCHIVE_PASSWORD`. |
| `tuwe_entries` | List entries in an ordinary archive. |
| `tuwe_test` | Test an ordinary archive entry by entry. |
| `tuwe_extract` | Extract an ordinary archive. `overwrite` is `overwrite`, `skip`, or `rename`. |
| `tuwe_issue` | Pack the files, then seal a payment archive. `logo.jpg` becomes `logo.jpg.kas`. |
| `tuwe_verify` | Check integrity, the issuer signature, and the blacklist. It does not pay. |
| `tuwe_list_entries` | List a payment archive. Before payment it returns the header and a preview. |
| `tuwe_query_license` | Read a purchase record on this computer. |
| `tuwe_wallet_status` | Read the wallet name, address, balance, and issue-grant state. It does not pay. |
| `tuwe_pay_extract` | Pay the archive price and extract. This spends KAS and requires `confirm=true`. |

Inputs and outputs must stay inside `TUWE_AGENT_ALLOW_DIRS`. If that variable is unset, the server uses `%LocalAppData%\TuweZip\agent-out`.

The price of a payment archive includes the software fee: about 0.2 KAS while the issuer's grant is current, and about 1 KAS when the grant is missing, expired, or the grant service cannot be reached. The fee is deducted before the issuer and rights-owner shares.

A client that auto-approves tool calls can run `tuwe_pay_extract` as soon as the model sets `confirm=true`.

## Client examples

Copy a fragment from [examples](examples) into the client file below. Replace `C:\TuweZip\mcp` and `C:\Users\you` with the real paths.

| Client | Example | Config file |
| --- | --- | --- |
| Grok | [examples/grok/config.toml](examples/grok/config.toml) | `%USERPROFILE%\.grok\config.toml` |
| Claude Desktop | [examples/claude-desktop/claude_desktop_config.json](examples/claude-desktop/claude_desktop_config.json) | `%APPDATA%\Claude\claude_desktop_config.json` |
| Claude Code | [examples/claude-code/.mcp.json](examples/claude-code/.mcp.json) | project `.mcp.json` |
| Cursor | [examples/cursor/mcp.json](examples/cursor/mcp.json) | `%USERPROFILE%\.cursor\mcp.json` |
| VS Code | [examples/vscode/mcp.json](examples/vscode/mcp.json) | `.vscode/mcp.json` |
| Windsurf | [examples/windsurf/mcp_config.json](examples/windsurf/mcp_config.json) | `%USERPROFILE%\.codeium\windsurf\mcp_config.json` |
| Cline | [examples/cline/cline_mcp_settings.json](examples/cline/cline_mcp_settings.json) | Cline MCP settings |
| Continue | [examples/continue/config.yaml](examples/continue/config.yaml) | `%USERPROFILE%\.continue\config.yaml` |
| Codex | [examples/codex/config.toml](examples/codex/config.toml) | `%USERPROFILE%\.codex\config.toml` |
| LM Studio | [examples/lm-studio/mcp.json](examples/lm-studio/mcp.json) | `%USERPROFILE%\.lmstudio\mcp.json` |
| Ollama via ollmcp | [examples/ollmcp/mcp.json](examples/ollmcp/mcp.json) | `%USERPROFILE%\.config\ollmcp\mcp.json` |
| AnythingLLM | [examples/anythingllm/anythingllm_mcp_servers.json](examples/anythingllm/anythingllm_mcp_servers.json) | `%APPDATA%\anythingllm-desktop\storage\plugins\anythingllm_mcp_servers.json` |
| Cherry Studio | [examples/cherry-studio/mcp.json](examples/cherry-studio/mcp.json) | Settings, MCP Servers, Import from JSON |
| Gemini CLI | [examples/gemini/settings.json](examples/gemini/settings.json) | `%USERPROFILE%\.gemini\settings.json` |
| Qwen Code | [examples/qwen/settings.json](examples/qwen/settings.json) | `%USERPROFILE%\.qwen\settings.json` |
| DeepSeek Harness | [examples/deepseek/cordis.patch.yml](examples/deepseek/cordis.patch.yml) | `%USERPROFILE%\.dsh\profiles\web\cordis.patch.yml` |
| 豆包工作 | [examples/doubao/connector.txt](examples/doubao/connector.txt) | 技能 · 连接器 · 伙伴, 新建自定义连接器 |
| TRAE | [examples/trae/mcp.json](examples/trae/mcp.json) | Settings, MCP |
| Kimi Code CLI | [examples/kimi/mcp.json](examples/kimi/mcp.json) | `%USERPROFILE%\.kimi-code\mcp.json` |

Grok reads a project `.grok/config.toml` before the user file. The project file in this repository does not exist, so the user file is the one to edit. Claude Code, Cursor, Gemini CLI, and Qwen Code can take the wallet password from an existing environment variable. VS Code asks for it with a masked prompt. Codex forwards `BIDXIN_WALLET_PASSWORD` from the environment. DeepSeek Harness reads the same variable with `!!js process.env.BIDXIN_WALLET_PASSWORD`. The other examples leave the value empty for you to fill locally.

LM Studio 0.3.17 or later reads the same `mcpServers` object as Cursor. Open the Program tab and choose Install, then Edit mcp.json. Paste only the `tuwezip` entry if that file already has other servers. LM Studio shows each tool call before it runs.

Ollama itself does not start an MCP server. Run a local model through [ollmcp](https://github.com/jonigl/mcp-client-for-ollama), which does. Save the example as the user-scope file and start `ollmcp`, or register it for every project:

```
ollmcp mcp add --scope user --env "TUWE_AGENT_ALLOW_DIRS=C:\Users\you\Desktop;C:\Users\you\Documents" tuwezip -- C:\TuweZip\mcp\tuwezip-mcp.exe
```

The quotes keep PowerShell from treating `;` as a command separator. Write `BIDXIN_WALLET_PASSWORD` into the saved JSON after this command. Do not put the password on the command line. `ollama launch claude` and `ollama launch codex` use the Claude Code and Codex examples above.

AnythingLLM starts this stdio server from its plugin file. In the desktop app, the version number in the corner opens the storage folder; the file is `plugins\anythingllm_mcp_servers.json`. Invoke the agent with `@agent`.

Cherry Studio imports the JSON from Settings, MCP Servers, Add, Import from JSON. Enable the server, then bind it to the agent that should call it.

Open WebUI accepts MCP only over Streamable HTTP. This release has no HTTP endpoint, so Open WebUI cannot launch `tuwezip-mcp.exe`. Use LM Studio, ollmcp, or AnythingLLM for a local model.

Gemini CLI, Qwen Code, DeepSeek Harness, 豆包工作, TRAE, and Kimi Code CLI can start this stdio program. The model chat websites have no launcher for a local exe. These examples contain no model API key. Keep that key in the client's own model settings.

Gemini CLI reads `%USERPROFILE%\.gemini\settings.json` for every project, and `.gemini\settings.json` for one project. Paste the `tuwezip` entry into `mcpServers`. Leave `trust` at `false`, so `tuwe_pay_extract` still asks before it spends KAS. `${BIDXIN_WALLET_PASSWORD}` expands from the environment that starts `gemini`. Gemini removes an inherited variable whose name contains `PASSWORD` unless this `env` entry names it. An unset variable expands to an empty string, and an empty password does not unlock the wallet. Set the variable before you start the CLI. `gemini mcp add` writes the project file unless you pass `-s user`.

Qwen Code uses the same `mcpServers` fields, including `cwd`, `env`, and `trust`. The user file is `%USERPROFILE%\.qwen\settings.json`. A current Qwen Code release also has a local file `.qwen\settings.local.json` and a project file `.mcp.json`. `qwen mcp add` without `--scope user` writes one of those. Leave `trust` at `false`. `${BIDXIN_WALLET_PASSWORD}` expands when the process starts, before Qwen Code loads a `.env` file, so set the variable in the shell that launches `qwen`.

DeepSeek Harness launches this exe. Add one `@deepseek-ai/dsh-mcp-client` row. For the web profile, replace the `[]` in `%USERPROFILE%\.dsh\profiles\web\cordis.patch.yml` with the patch, or run `dsh web --patch` on that file. `%USERPROFILE%\.dsh\cordis.patch.yml` applies to every profile. When the profile file already has other rows, add the `mcp-tuwezip` item under the existing `insert` list. Keep the Windows paths in single quotes. `serverName: tuwezip` exposes each tool as `mcp__tuwezip__` plus the tool name, for example `mcp__tuwezip__tuwe_pack`. `!!js process.env.BIDXIN_WALLET_PASSWORD` reads the variable from the environment that starts `dsh`. The patch sets `toolCallTimeoutMs` to 180000 because the client default of 60000 can expire while a payment call is still waiting.

豆包工作, including the current 豆包 desktop app, uses the form in `examples/doubao/connector.txt`. Choose STDIO. Add no arguments. The form has no working-directory field, and `kas_conn.dll` beside the exe is enough. Type the wallet password only in that form on this computer.

TRAE IDE takes the JSON from Settings, MCP. The `command` must not contain a space, and `C:\TuweZip\mcp\tuwezip-mcp.exe` has none. TRAE has no `cwd` field. `START_MCP_TIMEOUT_MS` and `RUN_MCP_TIMEOUT_MS` are strings, in milliseconds. TRAE CLI uses `trae_cli.yaml` instead. That fragment is [examples/trae/trae_cli.yaml](examples/trae/trae_cli.yaml). Edit the CLI file with `traecli config edit`.

Kimi Code CLI reads `%USERPROFILE%\.kimi-code\mcp.json`, or `$KIMI_CODE_HOME\mcp.json` when that variable is set. A project file `.kimi-code\mcp.json` overrides the same server name. The example includes `cwd`. Fill the password on this computer. Kimi's MCP docs do not define environment expansion, so the example leaves the value empty. An older Kimi CLI stores `%USERPROFILE%\.kimi\mcp.json` and adds a stdio server with `kimi mcp add --transport stdio`. Keep a Kimi API key out of `mcp.json`.

## Protocol

stdio, newline-delimited JSON-RPC. The server accepts `2024-11-05`, `2025-03-26`, `2025-06-18`, and `2025-11-25`. An unknown client version receives `2025-11-25`. There is no HTTP endpoint in this release.
