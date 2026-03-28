# CLAUDE.md

> Antigravity context — every new session reads this file first.
> For deep dives, follow links to `docs/*.md`.

## Quick Start

```bash
npm install              # Install dependencies (use npm, NOT pnpm — pnpm not installed locally)
npm run build            # Build with tsup to dist/
npm run auth             # Start OAuth server (port 3000) — opens browser for Microsoft login
npm run create-config    # Generate mcp.json from tokens.json
npm run cli              # Run MCP server via CLI wrapper
npm start                # Run MCP server directly
```

## What This Is

MCP server for Microsoft To Do via Microsoft Graph API v1.0.
Published on npm as `microsoft-todo-mcp-server` (v1.1.3).
Fork of [@jhirono/todomcp](https://github.com/jhirono/todomcp).

16 MCP tools: lists CRUD, tasks CRUD, checklist items CRUD, organized view, task archiving.

## Context Index (docs/)

| File | What's Inside |
|------|--------------|
| [architecture.md](docs/architecture.md) | Source file map, build pipeline, key patterns |
| [tech-stack.md](docs/tech-stack.md) | Dependencies, versions, APIs |
| [tools-reference.md](docs/tools-reference.md) | All 16 MCP tools with params |
| [auth-and-tokens.md](docs/auth-and-tokens.md) | OAuth flow, token sources, Azure app config, sensitive files |
| [current-status.md](docs/current-status.md) | Project state, known issues, potential next steps |
| [FOLDER_ORGANIZATION_GUIDE.md](docs/FOLDER_ORGANIZATION_GUIDE.md) | Emoji-based list categorization feature docs |

## Source Map (quick ref)

```
src/
├── todo-index.ts         # Core MCP server — 16 tools, makeGraphRequest(), organizeLists()
├── cli.ts                # CLI entry point — loads tokens from env or tokens.json
├── auth-server.ts        # Express OAuth 2.0 server with MSAL (port 3000)
├── token-manager.ts      # Token refresh logic (auto-refresh 5min before expiry)
├── create-mcp-config.ts  # Generates mcp.json from tokens.json
└── setup.ts              # Interactive setup wizard
```

## Critical Rules

- **Use `npm`, not `pnpm`** — pnpm is not installed on this machine
- **Never commit**: `tokens.json`, `mcp.json`, `.mcp.json`, `.env` (all in .gitignore)
- **ESM only** — `"type": "module"`, tsup bundles to ESM
- **Azure App ID**: `a40c703e-6bcf-4497-9fe7-447740295c65`, TENANT_ID: `common`
- **Graph API hierarchy**: Lists → Tasks → Checklist Items
- **Personal MS accounts** get `MailboxNotEnabledForRESTAPI` — handled gracefully, it's a Microsoft limitation
