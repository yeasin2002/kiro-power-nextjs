# Next.js DevTools MCP

A Kiro Power that connects coding agents to Next.js development tools for real-time diagnostics, automated upgrades, and comprehensive documentation access.

## Features

### Runtime Diagnostics (Next.js 16+)

- **Real-time error detection** - Get build errors, runtime errors, and TypeScript issues from your running dev server
- **Route inspection** - View all routes, page metadata, and component hierarchies
- **Live logs** - Access development server logs and browser console output
- **Server Actions lookup** - Find Server Actions by ID with source file locations

### Development Automation

- **Automated upgrades** - Upgrade to Next.js 16 with official codemods applied automatically
- **Cache Components setup** - Enable and configure Cache Components with error detection and fixes
- **Browser testing** - Integrated Playwright automation for visual verification

### Documentation Access

- **Searchable docs** - Query official Next.js documentation directly
- **Knowledge base** - 12 focused Next.js 16 resources on Cache Components, async APIs, and more
- **Best practices** - Get accurate, documentation-backed guidance

## Quick Start

1. Install this power in Kiro
2. Start your Next.js dev server: `npm run dev`
3. Initialize context: `Use the init tool to set up Next.js DevTools context`
4. Try it: `Next Devtools, what errors are in my application?`

## Requirements

- Next.js 16+ (for runtime diagnostics)
- Node.js 16+
- Running dev server for live features

## Example Prompts

```
Next Devtools, what errors are currently in my application?
Next Devtools, show me the structure of my routes
Next Devtools, help me upgrade my Next.js app to version 16
Next Devtools, enable Cache Components in my Next.js app
Next Devtools, search Next.js docs for generateMetadata
```

## How It Works

This power connects to your Next.js 16+ dev server's built-in MCP endpoint at `/_next/mcp`, providing direct access to your application's runtime state. For all Next.js versions, it offers development automation tools and documentation search.

## License

MIT
