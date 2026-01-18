# Project Structure

## Root Files

- **`POWER.md`** - Comprehensive power documentation including onboarding, workflows, troubleshooting, and configuration
- **`README.md`** - Quick start guide and feature overview for users
- **`mcp.json`** - MCP server configuration for Kiro integration

## Steering Directory

Located at `steering/` (should be `.kiro/steering/` per Kiro conventions):

- **`cache-components.md`** - Complete guide for enabling and using Cache Components in Next.js 16
- **`debugging-errors.md`** - Step-by-step debugging workflow using runtime diagnostics
- **`upgrading-nextjs.md`** - Comprehensive Next.js 16 upgrade guide with codemods and troubleshooting

## Documentation Organization

### POWER.md Structure

- Overview and onboarding
- Available MCP tools (7 core tools + 6 runtime diagnostic tools)
- Common workflows (6 detailed workflows)
- Best practices
- Troubleshooting
- Advanced usage
- Configuration

### Steering Files Pattern

Each steering file follows this structure:

1. Overview and prerequisites
2. Step-by-step procedures
3. Code examples (before/after patterns)
4. Common issues and solutions
5. Best practices
6. Summary

## MCP Tools Organization

### Core Tools (Always Available)

- `init` - Initialize context
- `nextjs_docs` - Search documentation
- `nextjs_index` - Discover servers
- `nextjs_call` - Execute runtime tools
- `browser_eval` - Browser automation
- `upgrade_nextjs_16` - Automated upgrades
- `enable_cache_components` - Cache setup

### Runtime Tools (Next.js 16+ Only)

Accessed via `nextjs_call`:

- `get_errors` - Build/runtime/TypeScript errors
- `get_logs` - Dev server and console logs
- `get_page_metadata` - Page structure and rendering
- `get_project_metadata` - Project configuration
- `get_routes` - All application routes
- `get_server_action_by_id` - Server Action lookup

## Conventions

- All documentation uses markdown
- Code examples show before/after patterns for clarity
- Troubleshooting sections include cause and solution
- Commands are provided for multiple package managers (npm/yarn/pnpm)
- Examples use TypeScript syntax
- File paths use forward slashes (cross-platform)
