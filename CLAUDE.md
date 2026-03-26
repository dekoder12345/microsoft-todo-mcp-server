# CLAUDE.md

## Commands

```bash
npm install              # Install dependencies
npm run build            # Build with tsup to dist/
npm run auth             # Start OAuth server (port 3000) — opens browser for Microsoft login
npm run create-config    # Generate mcp.json from tokens.json
npm run cli              # Run MCP server via CLI wrapper
npm start                # Run MCP server directly
```

pnpm is not installed on this machine — use npm instead.

## Architecture

MCP server for Microsoft To Do via Microsoft Graph API v1.0.

```
src/
├── todo-index.ts         # Core MCP server — 15 tools for lists, tasks, checklist items
├── cli.ts                # CLI entry point — loads tokens from env or tokens.json
├── auth-server.ts        # Express OAuth 2.0 server with MSAL (port 3000)
├── token-manager.ts      # Token refresh logic (auto-refresh 5min before expiry)
├── create-mcp-config.ts  # Generates mcp.json from tokens.json
└── setup.ts              # Interactive setup wizard
```

Graph API hierarchy: Lists → Tasks → Checklist Items

## Token Flow

1. `npm run auth` → browser login → tokens saved to `tokens.json`
2. `npm run create-config` → reads `tokens.json` → writes `mcp.json`
3. Copy token env vars from `mcp.json` into `.mcp.json` for Claude Code
4. Access tokens expire in ~1h; refresh token handles renewal automatically

## Sensitive Files (never commit)

- `tokens.json` — access + refresh tokens
- `mcp.json` / `.mcp.json` — MCP config with tokens in env vars
- `.env` — CLIENT_ID, CLIENT_SECRET, TENANT_ID, REDIRECT_URI

All listed in `.gitignore`.

## Azure App Registration

- App ID (CLIENT_ID): `a40c703e-6bcf-4497-9fe7-447740295c65`
- TENANT_ID: `common` (personal + org accounts)
- Redirect URI: `http://localhost:3000/callback`
- Required permissions: Tasks.Read, Tasks.ReadWrite, User.Read

## Key Patterns

- TypeScript strict mode with Zod schemas for tool parameter validation
- ESM modules (`"type": "module"`)
- tsup bundles to `dist/`
- Personal Microsoft accounts get `MailboxNotEnabledForRESTAPI` — handled gracefully
- Token refresh happens automatically via refresh token in token-manager.ts
