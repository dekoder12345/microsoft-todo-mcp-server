# Authentication & Token Flow

## Azure App Registration
- **App ID (CLIENT_ID)**: `a40c703e-6bcf-4497-9fe7-447740295c65`
- **TENANT_ID**: `common` (personal + org accounts)
- **Redirect URI**: `http://localhost:3000/callback`
- **Permissions**: Tasks.Read, Tasks.ReadWrite, Tasks.Read.Shared, Tasks.ReadWrite.Shared, User.Read

## Token Flow

```
1. npm run auth
   → Express server on :3000
   → Browser redirect to Microsoft login
   → /callback receives auth code
   → MSAL exchanges code for tokens
   → Saves to tokens.json (with client credentials for future refresh)

2. npm run create-config
   → Reads tokens.json
   → Writes mcp.json with MS_TODO_ACCESS_TOKEN + MS_TODO_REFRESH_TOKEN

3. MCP server startup (cli.ts)
   → Checks env vars (MS_TODO_ACCESS_TOKEN, MS_TODO_REFRESH_TOKEN)
   → Falls back to tokens.json in CWD
   → TokenManager handles refresh lifecycle

4. Runtime token refresh (token-manager.ts)
   → Checks expiry (5 min buffer)
   → Uses refresh_token grant to get new access token
   → Saves updated tokens
   → Auto-updates Claude Desktop config
```

## Token Sources (priority order)
1. Environment variables: `MS_TODO_ACCESS_TOKEN` + `MS_TODO_REFRESH_TOKEN`
2. Config dir: `%APPDATA%/microsoft-todo-mcp/tokens.json` (Win) | `~/.config/microsoft-todo-mcp/tokens.json` (Unix)
3. Legacy: `tokens.json` in CWD (auto-migrates to config dir)

## Sensitive Files (never commit)
- `tokens.json` — access + refresh tokens + client credentials
- `mcp.json` / `.mcp.json` — MCP config with tokens in env vars
- `.env` — CLIENT_ID, CLIENT_SECRET, TENANT_ID, REDIRECT_URI

All listed in `.gitignore`.

## Known Limitations
- Personal MS accounts (outlook.com, hotmail.com, live.com) get `MailboxNotEnabledForRESTAPI`
- This is a Microsoft Graph API limitation, not a bug
- Work/school accounts have full API access
- Access tokens expire in ~1h; refresh token handles renewal automatically
