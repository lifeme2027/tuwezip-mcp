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

Grok reads a project `.grok/config.toml` before the user file. The project file in this repository does not exist, so the user file is the one to edit. Claude Code and Cursor can take the wallet password from an existing environment variable. VS Code asks for it with a masked prompt. Codex forwards `BIDXIN_WALLET_PASSWORD` from the environment. The other examples leave the value empty for you to fill locally.

## Protocol

stdio, newline-delimited JSON-RPC. The server accepts `2024-11-05`, `2025-03-26`, `2025-06-18`, and `2025-11-25`. An unknown client version receives `2025-11-25`. There is no HTTP endpoint in this release.
