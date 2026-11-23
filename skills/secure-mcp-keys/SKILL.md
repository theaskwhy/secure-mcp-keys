---
name: secure-mcp-keys
description: Use when connecting a local MCP server that needs an access token (e.g. a desktop app's automation server) to Claude Code, Codex or other agents — keeping the token in the macOS Keychain instead of chat, files or shell history.
---

# Secure MCP Keys (macOS)

Connect token-protected local MCP servers to AI agents without leaking the token. By The Ask Why Company.

## Steps
1. **Store the token once** (the prompt hides input):
   `security add-generic-password -U -a "$USER" -s <service-name> -w`
2. **Claude Code** — read from Keychain at add time; output redacts the header:
   `claude mcp add --transport http --scope user <name> http://127.0.0.1:<port>/mcp --header "Authorization: Bearer $(security find-generic-password -s <service-name> -w)"`
3. **Codex** — register with an env-var token, and export that var from Keychain in the script that launches Codex:
   `codex mcp add <name> --url http://127.0.0.1:<port>/mcp --bearer-token-env-var MY_TOKEN`
   `export MY_TOKEN="$(security find-generic-password -s <service-name> -w)"`
4. **Verify:** `claude mcp get <name>` shows Connected. Test read-only first (list tools/projects).

## Rules
- Never paste a token into chat, tickets, docs or config files you share. If it was exposed, **regenerate it** in the app, update the Keychain item, and re-add the clients.
- Keep the server bound to localhost; turn automation off when not in use.
- Agents get the token only through the environment of the process that needs it.
