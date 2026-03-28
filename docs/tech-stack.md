# Tech Stack

## Runtime & Language
- **Node.js** 18+ (tested: 18.x, 20.x, 22.x)
- **TypeScript** 5.9 (strict mode)
- **ESM modules** (`"type": "module"` in package.json)

## Core Dependencies
| Package | Version | Purpose |
|---------|---------|---------|
| `@modelcontextprotocol/sdk` | ^1.21.1 | MCP server framework (McpServer, StdioServerTransport) |
| `@azure/msal-node` | ^3.8.1 | Microsoft OAuth 2.0 (ConfidentialClientApplication) |
| `express` | ^5.1.0 | Auth server HTTP framework |
| `dotenv` | ^16.6.1 | Environment variable loading |
| `zod` | ^3.25.76 | Tool parameter validation schemas |

## Dev Dependencies
| Package | Version | Purpose |
|---------|---------|---------|
| `tsup` | ^8.5.0 | Bundle TS → ESM (dist/) |
| `typescript` | ^5.9.3 | Type checking |
| `prettier` | ^3.6.2 | Code formatting |
| `@types/express` | ^5.0.5 | Express type definitions |
| `@types/node` | ^22.19.0 | Node.js type definitions |

## APIs
- **Microsoft Graph API v1.0** — `/me/todo/lists`, `/me/todo/lists/{id}/tasks`, checklist items
- **Microsoft Identity Platform** — OAuth 2.0 authorization code flow with MSAL

## Build & CI
- **tsup** — bundles to `dist/`, ESM format, sourcemaps, .d.ts generation
- **GitHub Actions** — CI on push/PR to main, format check separate job
- **Package manager**: pnpm (in CI/repo config), npm locally (pnpm not installed on dev machine)

## Important Notes
- `package.json` scripts reference `pnpm` but **use `npm` locally** (pnpm not available)
- `pnpm-lock.yaml` exists for CI; `package-lock.json` is gitignored
