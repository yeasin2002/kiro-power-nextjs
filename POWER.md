---
name: "nextjs-devtools"
displayName: "Next.js DevTools MCP"
description: "Connect to Next.js 16+ dev servers for real-time error detection, route inspection, and automated upgrades with comprehensive runtime diagnostics and development automation."
keywords:
  [
    "nextjs",
    "next.js",
    "react",
    "devtools",
    "debugging",
    "mcp",
    "runtime",
    "errors",
  ]
author: "Vercel"
---

# Next.js DevTools MCP

## Overview

Next.js DevTools MCP is a powerful Model Context Protocol server that bridges coding agents with Next.js development tools. It provides three core capabilities: runtime diagnostics for live application state, development automation for upgrades and migrations, and comprehensive Next.js documentation access.

For Next.js 16+ projects, this power connects directly to your running dev server's built-in MCP endpoint at `/_next/mcp`, giving you real-time access to build errors, runtime errors, TypeScript issues, application routes, Server Actions, and development logs. For all Next.js versions, it provides automated upgrade tools, Cache Components setup, browser testing integration, and searchable Next.js documentation.

This power is essential for Next.js developers who want to leverage AI assistance for debugging, upgrading, and understanding their applications with accurate, real-time information.

## Onboarding

### Prerequisites

- **Next.js 16+** (required for runtime diagnostics features)
- **Node.js 16+** (for running the MCP server)
- **Running Next.js dev server** (`npm run dev`) to access runtime features
- **npx** (comes with npm 5.2+)

### Installation

The MCP server is automatically installed via npx when configured in Kiro. No manual installation required.

### Configuration

After installing this power in Kiro, the MCP server will be available. To use runtime diagnostics features:

1. **Start your Next.js dev server:**

   ```bash
   npm run dev
   ```

2. **Initialize the MCP context** (recommended at start of each session):

   ```
   Use the init tool to set up Next.js DevTools context
   ```

3. **Verify connection** (for Next.js 16+ projects):
   The MCP server automatically discovers running Next.js instances at `http://localhost:PORT/_next/mcp`

### First Steps

**For Next.js 16+ projects with running dev server:**

```
Next Devtools, what errors are in my Next.js application?
Next Devtools, show me the structure of my routes
Next Devtools, what's in the development server logs?
```

**For all Next.js projects:**

```
Next Devtools, help me upgrade my Next.js app to version 16
Next Devtools, search Next.js docs for generateMetadata
Next Devtools, enable Cache Components in my Next.js app
```

## Available MCP Tools

This power provides access to the following MCP tools through the `next-devtools` server:

### Core Tools

- **`init`** - Initialize Next.js DevTools context and establish documentation requirements
- **`nextjs_docs`** - Search and retrieve official Next.js documentation
- **`nextjs_index`** - Discover all running Next.js dev servers and list available tools
- **`nextjs_call`** - Execute specific runtime diagnostic tools on Next.js dev servers
- **`browser_eval`** - Automate browser testing using Playwright integration
- **`upgrade_nextjs_16`** - Guide through Next.js 16 upgrade with automated codemods
- **`enable_cache_components`** - Complete Cache Components setup with error detection

### Runtime Diagnostic Tools (Next.js 16+ via nextjs_call)

When connected to a running Next.js 16+ dev server, these tools become available:

- **`get_errors`** - Retrieve build errors, runtime errors, and TypeScript type errors
- **`get_logs`** - Access development server logs and browser console output
- **`get_page_metadata`** - Get metadata about specific pages (routes, components, rendering)
- **`get_project_metadata`** - Retrieve project structure, configuration, and dev server URL
- **`get_routes`** - List all routes grouped by router type (App Router, Pages Router)
- **`get_server_action_by_id`** - Look up Server Actions by ID to find source files

## Common Workflows

### Workflow 1: Debug Runtime Errors

**Goal:** Identify and fix errors in your running Next.js application

**Prerequisites:** Next.js 16+ dev server running (`npm run dev`)

**Steps:**

1. **Initialize context:**

   ```
   Use the init tool to set up Next.js DevTools context
   ```

2. **Check for errors:**

   ```
   Next Devtools, what errors are currently in my application?
   ```

3. **Get detailed error information:**
   The agent will use `nextjs_call` with `get_errors` tool to retrieve:
   - Build errors with file locations
   - Runtime errors with stack traces
   - TypeScript type errors with line numbers

4. **Analyze and fix:**
   The agent can analyze errors and suggest fixes based on:
   - Error messages and stack traces
   - File locations and line numbers
   - Next.js best practices from documentation

**Example Output:**

```
Found 2 errors in your Next.js application:

1. Runtime Error (Hydration):
   - Location: app/about/page.tsx:15
   - Issue: Server rendered "server" but client rendered "client"
   - Fix: Ensure consistent rendering between server and client

2. TypeScript Error:
   - Location: components/Header.tsx:23
   - Issue: Property 'title' does not exist on type 'Props'
   - Fix: Add 'title' to Props interface
```

### Workflow 2: Inspect Application Routes

**Goal:** Understand your application's routing structure

**Prerequisites:** Next.js 16+ dev server running

**Steps:**

1. **Query all routes:**

   ```
   Next Devtools, show me all routes in my application
   ```

2. **The agent uses `nextjs_call` with `get_routes` to retrieve:**
   - App Router routes (app directory)
   - Pages Router routes (pages directory)
   - Dynamic route segments (`[param]`, `[...slug]`)
   - Route groups and parallel routes

3. **Get specific page details:**

   ```
   Next Devtools, show me metadata for the /dashboard page
   ```

4. **The agent uses `get_page_metadata` to retrieve:**
   - Page components and layouts
   - Rendering strategy (static, dynamic, streaming)
   - Server Components vs Client Components
   - Data fetching patterns

**Example Output:**

```
App Router Routes:
- / (app/page.tsx)
- /about (app/about/page.tsx)
- /blog/[slug] (app/blog/[slug]/page.tsx)
- /dashboard (app/dashboard/page.tsx)

Pages Router Routes:
- /api/hello (pages/api/hello.ts)
```

### Workflow 3: Upgrade to Next.js 16

**Goal:** Safely upgrade your Next.js application to version 16

**Prerequisites:** Existing Next.js project (any version)

**Steps:**

1. **Start upgrade process:**

   ```
   Next Devtools, help me upgrade my Next.js app to version 16
   ```

2. **The agent uses `upgrade_nextjs_16` tool which:**
   - Analyzes current Next.js version
   - Identifies required changes
   - Runs official Next.js codemods automatically
   - Updates dependencies in package.json

3. **Review changes:**
   - Check modified files
   - Review breaking changes
   - Test application functionality

4. **Handle async API changes:**
   The upgrade tool automatically applies codemods for:
   - Async `params` in page components
   - Async `searchParams` in page components
   - Async `cookies()` and `headers()` APIs
   - Dynamic route parameters

5. **Verify upgrade:**
   ```bash
   npm install
   npm run dev
   ```

**Common Upgrade Changes:**

```typescript
// Before (Next.js 15)
export default function Page({ params }) {
  return <div>{params.id}</div>
}

// After (Next.js 16)
export default async function Page({ params }) {
  const { id } = await params
  return <div>{id}</div>
}
```

### Workflow 4: Enable Cache Components

**Goal:** Set up and configure Cache Components in Next.js 16

**Prerequisites:** Next.js 16+ project

**Steps:**

1. **Start Cache Components setup:**

   ```
   Next Devtools, enable Cache Components in my Next.js app
   ```

2. **The agent uses `enable_cache_components` tool which:**
   - Updates `next.config.js` with `experimental.cacheComponents: true`
   - Detects caching errors via browser automation
   - Applies automated fixes:
     - Adds Suspense boundaries where needed
     - Adds `use cache` directives
     - Configures static parameters for dynamic routes

3. **Verify setup:**
   - Start dev server: `npm run dev`
   - Check for caching errors
   - Test page rendering

4. **Understand caching patterns:**
   ```
   Next Devtools, search docs for Cache Components best practices
   ```

**Cache Components Patterns:**

```typescript
// Public cache (shared across users)
"use cache";
export async function getPublicData() {
  return await fetch("https://api.example.com/public");
}

// Private cache (per-user)
("use cache: private");
export async function getUserData() {
  const session = await getSession();
  return await fetch(`https://api.example.com/user/${session.id}`);
}
```

### Workflow 5: Search Next.js Documentation

**Goal:** Find specific Next.js documentation and best practices

**Prerequisites:** None (works without running dev server)

**Steps:**

1. **Search documentation:**

   ```
   Next Devtools, search Next.js docs for [topic]
   ```

   Examples:
   - "generateMetadata"
   - "Server Actions"
   - "dynamic routes"
   - "middleware"

2. **The agent uses `nextjs_docs` tool to:**
   - Search official Next.js documentation
   - Access Next.js 16 knowledge base
   - Retrieve relevant code examples
   - Provide best practices

3. **Get detailed explanations:**
   The agent can explain concepts with:
   - Official documentation references
   - Code examples
   - Common patterns
   - Migration guides

**Example Topics:**

- Metadata API and SEO
- Server Components vs Client Components
- Data fetching patterns
- Route handlers and API routes
- Middleware configuration
- Image optimization
- Font optimization

### Workflow 6: Browser Testing with Playwright

**Goal:** Automate browser testing for Next.js pages

**Prerequisites:** Next.js dev server running

**Steps:**

1. **Navigate to page:**

   ```
   Next Devtools, open my homepage in the browser and check for errors
   ```

2. **The agent uses `browser_eval` tool to:**
   - Launch Playwright browser
   - Navigate to specified URL
   - Capture screenshots
   - Check console errors
   - Verify page rendering

3. **Test interactions:**

   ```
   Next Devtools, click the login button and verify the form appears
   ```

4. **Verify visual appearance:**
   - Take screenshots
   - Compare layouts
   - Check responsive design

**Available Browser Actions:**

- `navigate` - Go to specific URL
- `screenshot` - Capture page screenshot
- `click` - Click elements
- `type` - Enter text in inputs
- `console_messages` - Get browser console logs

## Best Practices

### Always Initialize Context

At the start of each Next.js session, call the `init` tool to:

- Establish proper documentation context
- Ensure accurate Next.js information
- Enable optimal tool usage

**Pro Tip:** Add auto-initialization to your agent config:

```markdown
When starting work on a Next.js project, ALWAYS call the init tool from
next-devtools-mcp FIRST to set up proper context.
```

### Prioritize Runtime Tools Over Browser Console

For Next.js 16+ projects:

- Use `nextjs_call` with `get_errors` instead of browser console logs
- Use `get_logs` for development server output
- Use `get_page_metadata` for component information
- Only fall back to `browser_eval` when runtime tools aren't available

### Keep Dev Server Running

Runtime diagnostic features require a running dev server:

```bash
npm run dev
```

The MCP server automatically discovers servers on common ports (3000, 3001, etc.)

### Use Specific Tool Names

When calling runtime tools via `nextjs_call`, use exact tool names:

- `get_errors` (not "getErrors" or "errors")
- `get_routes` (not "getRoutes" or "routes")
- `get_page_metadata` (not "pageMetadata")

### Leverage Knowledge Base Resources

The power includes 12 focused Next.js 16 knowledge base resources:

- Core mechanics and paradigm shifts
- Public and private cache patterns
- Async request APIs
- Cache invalidation strategies
- Error patterns and solutions
- Test patterns from 125+ fixtures

These are loaded on-demand by the agent for efficient context management.

## Troubleshooting

### Error: "Module not found" or "Cannot find module"

**Cause:** npx cache issue or corrupted installation

**Solution:**

```bash
# Clear npx cache
npx clear-npx-cache

# Or manually clear
rm -rf ~/.npm/_npx

# Restart your MCP client (Kiro)
```

### Error: "No server info found"

**Cause:** Next.js dev server not running or not Next.js 16+

**Solutions:**

1. **Start dev server:**

   ```bash
   npm run dev
   ```

2. **Verify Next.js version:**

   ```bash
   npm list next
   ```

   Must be 16.0.0 or higher for runtime features

3. **Check server started successfully:**
   Look for "Ready" message in terminal

4. **Verify port:**
   Default is 3000, but check your terminal output

**Note:** Runtime tools (`nextjs_index`, `nextjs_call`) require Next.js 16+ with running dev server. Other tools (`nextjs_docs`, `upgrade_nextjs_16`, `enable_cache_components`) work without a running server.

### Error: "Connection refused" or "ECONNREFUSED"

**Cause:** Dev server not accessible or wrong port

**Solutions:**

1. **Check dev server is running:**

   ```bash
   curl http://localhost:3000/_next/mcp
   ```

2. **Verify port number:**
   Check terminal output for actual port (might be 3001, 3002, etc.)

3. **Check firewall settings:**
   Ensure localhost connections are allowed

4. **Restart dev server:**
   ```bash
   # Stop server (Ctrl+C)
   npm run dev
   ```

### Runtime Tools Not Available

**Cause:** Using Next.js 15 or earlier

**Solution:**

Upgrade to Next.js 16:

```
Next Devtools, help me upgrade my Next.js app to version 16
```

The `upgrade_nextjs_16` tool will:

- Update dependencies
- Run codemods
- Handle breaking changes
- Guide through migration

### Browser Automation Fails

**Cause:** Playwright not installed or browser issues

**Solutions:**

1. **Install Playwright:**

   ```bash
   npx playwright install
   ```

2. **Install browser dependencies:**

   ```bash
   npx playwright install-deps
   ```

3. **Check browser availability:**
   ```bash
   npx playwright --version
   ```

### Slow Tool Response

**Cause:** Large project or many errors

**Solutions:**

1. **Fix critical errors first:**
   Focus on build errors before runtime errors

2. **Use specific queries:**
   Instead of "show all errors", ask for specific page errors

3. **Restart dev server:**
   Clear build cache and restart

### TypeScript Errors Not Showing

**Cause:** TypeScript not configured or type checking disabled

**Solutions:**

1. **Verify TypeScript setup:**

   ```bash
   npx tsc --version
   ```

2. **Check tsconfig.json exists:**
   Ensure TypeScript is configured

3. **Enable type checking in Next.js:**
   ```javascript
   // next.config.js
   module.exports = {
     typescript: {
       ignoreBuildErrors: false,
     },
   };
   ```

## Advanced Usage

### Auto-Discovery of Multiple Dev Servers

The MCP server can discover multiple Next.js instances:

```
Next Devtools, list all running Next.js servers
```

Uses `nextjs_index` to scan common ports and show:

- Port numbers
- Available tools per server
- Project paths

### Custom Port Configuration

If using non-standard ports, specify explicitly:

```
Next Devtools, get errors from the server on port 4000
```

The agent will use `nextjs_call` with `port: 4000` parameter.

### Combining Tools for Complex Workflows

Example: Debug and fix hydration errors

1. Get errors: `nextjs_call` with `get_errors`
2. Get page metadata: `nextjs_call` with `get_page_metadata`
3. Search docs: `nextjs_docs` for "hydration"
4. Verify fix: `browser_eval` to test page

### Knowledge Base Resources

Access specific Next.js 16 knowledge:

- `nextjs16://knowledge/overview` - Overview and common mistakes
- `nextjs16://knowledge/core-mechanics` - Cache Components fundamentals
- `nextjs16://knowledge/error-patterns` - Common errors and fixes
- `nextjs16://knowledge/test-patterns` - Testing strategies

These are automatically loaded by the agent when relevant.

## Configuration

### Environment Variables

**Disable Telemetry (Optional):**

```bash
export NEXT_DEVTOOLS_MCP_TELEMETRY=0
```

Add to shell config (~/.bashrc, ~/.zshrc) to make permanent.

**Telemetry Data Collected:**

- Tool usage (anonymous)
- Error types (no sensitive data)
- Performance metrics

**Telemetry NOT Collected:**

- Source code
- File contents
- Environment variables
- Personal information

**Delete Telemetry Data:**

```bash
rm -rf ~/.next-devtools-mcp/
```

### MCP Client Configuration

The mcp.json configuration uses npx for automatic updates:

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

**Using @latest ensures:**

- Always get newest features
- Automatic bug fixes
- Latest Next.js documentation
- New tool capabilities

### Local Development Setup

For contributing or testing local changes:

```json
{
  "mcpServers": {
    "next-devtools": {
      "command": "node",
      "args": ["/absolute/path/to/next-devtools-mcp/dist/stdio.js"]
    }
  }
}
```

## Additional Resources

- **Official Documentation:** [Next.js MCP Guide](https://nextjs.org/docs/app/guides/mcp)
- **GitHub Repository:** [vercel/next-devtools-mcp](https://github.com/vercel/next-devtools-mcp)
- **MCP Servers Directory:** [Next.js DevTools MCP](https://mcpservers.org/servers/vercel/next-devtools-mcp)
- **Next.js Documentation:** [nextjs.org/docs](https://nextjs.org/docs)
- **Model Context Protocol:** [modelcontextprotocol.io](https://modelcontextprotocol.io/)

---

**Package:** `next-devtools-mcp@latest`  
**MCP Server:** `next-devtools`  
**License:** MIT  
**Maintained by:** Vercel
