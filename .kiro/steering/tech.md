# Technology Stack

## Core Technologies

- **MCP (Model Context Protocol)** - Protocol for connecting AI agents to development tools
- **Next.js 16+** - Target framework for runtime diagnostics features
- **Node.js 16+** - Runtime environment
- **Playwright** - Browser automation for testing
- **TypeScript** - Primary language (inferred from documentation)

## Package Distribution

- **Package name:** `next-devtools-mcp`
- **Distribution:** npm via npx
- **Installation:** Automatic via `npx -y next-devtools-mcp@latest`
- **Updates:** Always uses `@latest` for automatic updates

## MCP Server Configuration

The power is configured in `mcp.json`:

```json
{
  "mcpServers": {
    "next-devtools": {
      "command": "npx",
      "args": ["-y", "next-devtools-mcp@latest"]
    }
  }
}
```

## Common Commands

### For Next.js Projects (Users)

```bash
# Start Next.js dev server (required for runtime features)
npm run dev

# Build for production
npm run build

# Install Playwright for browser automation
npx playwright install
npx playwright install-deps
```

### For MCP Server Development

```bash
# Clear npx cache if issues occur
npx clear-npx-cache

# Or manually
rm -rf ~/.npm/_npx
```

## Architecture

- **MCP Server:** Runs via npx, connects to Next.js dev servers
- **Next.js Endpoint:** `http://localhost:PORT/_next/mcp` (Next.js 16+ built-in)
- **Auto-discovery:** Scans common ports (3000, 3001, etc.) for running Next.js instances
- **Communication:** MCP protocol over stdio

## Requirements

- Next.js 16+ for runtime diagnostics
- Next.js any version for automation tools
- Running dev server for live features
- npx (comes with npm 5.2+)

## Knowledge Base

Includes 12 curated Next.js 16 resources loaded on-demand:

- Cache Components patterns
- Async API migrations
- Error patterns and solutions
- Test fixtures (125+ examples)
