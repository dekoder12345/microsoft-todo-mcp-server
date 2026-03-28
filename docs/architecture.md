# Architecture

## Overview

MCP (Model Context Protocol) server enabling AI assistants (Claude, Cursor) to manage Microsoft To Do tasks via Microsoft Graph API v1.0.

## Data Hierarchy

```
Microsoft To Do
└── Task Lists (top-level containers: "Work", "Personal", etc.)
    └── Tasks (main todo items with title, due date, status, etc.)
        └── Checklist Items (subtasks within a task)
```

## Source Files

```
src/
├── todo-index.ts          # Core MCP server — 16 tools (lists, tasks, checklist items, organization)
│                          # Exports: startServer(), McpServer instance
│                          # Key helper: makeGraphRequest<T>() with auto 401 retry
│                          # Key helper: getAccessToken() via tokenManager
│                          # Key helper: organizeLists() — emoji/pattern-based list categorization
│
├── cli.ts                 # CLI entry point (shebang script)
│                          # Loads tokens from env vars OR tokens.json fallback
│                          # Calls startServer() from todo-index.ts
│
├── auth-server.ts         # Express OAuth 2.0 server (port 3000)
│                          # MSAL ConfidentialClientApplication
│                          # Routes: / (landing), /auth (redirect to MS login), /callback (token exchange)
│                          # Routes: /refresh (silent token refresh), /silentLogin (client credentials)
│                          # Saves tokens + client credentials to tokens.json
│
├── token-manager.ts       # TokenManager class (singleton export: tokenManager)
│                          # Token sources: env vars → config dir → legacy CWD tokens.json
│                          # Auto-refresh via refresh_token grant
│                          # Auto-updates Claude Desktop config on refresh
│                          # Config dir: %APPDATA%/microsoft-todo-mcp (Win) | ~/.config/microsoft-todo-mcp (Unix)
│
├── create-mcp-config.ts   # Reads tokens.json → writes mcp.json with env vars for Claude/Cursor
│
└── setup.ts               # Interactive setup wizard (readline)
                           # Creates .env, starts auth-server, migrates tokens, updates Claude config
```

## Build Pipeline

- **Bundler**: tsup (ESM output to `dist/`)
- **TypeScript**: strict mode, ES2022 target, Node16 module resolution
- **Linting**: Prettier only (no ESLint)
- **CI**: GitHub Actions — Node 18/20/22 matrix, pnpm, lint + typecheck + build

## Key Patterns

- All tools use Zod schemas for parameter validation
- `makeGraphRequest()` handles 401 → auto token refresh → retry
- Personal MS accounts get `MailboxNotEnabledForRESTAPI` — detected and warned gracefully
- Token refresh happens 5 min before expiry (buffer built into expiresAt calculation)
- Server logs to stderr (MCP protocol uses stdout for JSON-RPC)

## npm Package

- Published as `microsoft-todo-mcp-server` on npm
- Binary aliases: `microsoft-todo-mcp-server`, `mstodo`, `mstodo-config`, `mstodo-setup`
- Fork of [@jhirono/todomcp](https://github.com/jhirono/todomcp)
