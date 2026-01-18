# Debugging Errors with Next.js DevTools

This steering file provides detailed guidance for debugging Next.js applications using runtime diagnostics.

## Prerequisites

- Next.js 16+ project
- Dev server running (`npm run dev`)
- MCP context initialized (`init` tool)

## Step-by-Step Debugging Workflow

### 1. Initialize Context

Always start by initializing the Next.js DevTools context:

```
Use the init tool to set up Next.js DevTools context
```

This ensures the agent has access to official Next.js documentation and proper tool configuration.

### 2. Discover Running Servers

Use `nextjs_index` to find all running Next.js dev servers:

```json
{
  "action": "discover_servers"
}
```

This returns:

- Port numbers of running servers
- Available runtime tools per server
- Project paths

### 3. Get Current Errors

Use `nextjs_call` with `get_errors` tool:

```json
{
  "action": "call_tool",
  "port": 3000,
  "toolName": "get_errors"
}
```

This retrieves:

- **Build errors** - Compilation failures with file locations
- **Runtime errors** - JavaScript errors with stack traces
- **TypeScript errors** - Type checking issues with line numbers

### 4. Analyze Error Types

#### Build Errors

- Usually indicate syntax errors or import issues
- Fix these first as they block the application
- Check furing application execution
- Common types:
  - Hydration mismatches (server vs client rendering)
  - Undefined variables or functions
  - API call failures
  - Component lifecycle issues

#### TypeScript Errors

- Type checking violations
- Missing type definitions
- Incorrect prop types
- Generic type mismatches

### 5. Get Additional Context

For specific pages with errors, use `get_page_metadata`:

```json
{
  "action": "call_tool",
  "port": 3000,
  "toolName": "get_page_metadata",
  "args": {
    "path": "/about"
  }
}
```

This provides:

- Component hierarchy
- Rendering strategy (static/dynamic)
- Server vs Client Components
- Data fetching patterns

### 6. Check Development Logs

Use `get_logs` to access full development output:

```json
{
  "action": "call_tool",
  "port": 3000,
  "toolName": "get_logs"
}
```

Returns path to log file containing:

- Browser console logs
- Server-side console output
- Build warnings
- Network requests

## Common Error Patterns and Solutions

### Hydration Mismatch

**Error:** "Text content does not match server-rendered HTML"

**Cause:** Server renders different content than client

**Solution:**

1. Check for browser-only APIs (window, document) in Server Components
2. Ensure consistent data between server and client
3. Use `useEffect` for client-only rendering
4. Add `'use client'` directive if needed

**Example Fix:**

```typescript
// Before (causes hydration error)
export default function Page() {
  return <div>{new Date().toLocaleString()}</div>
}

// After (fixed)
'use client'
import { useEffect, useState } from 'react'

export default function Page() {
  const [time, setTime] = useState('')

  useEffect(() => {
    setTime(new Date().toLocaleString())
  }, [])

  return <div>{time}</div>
}
```

### Async Request APIs Error

**Error:** "params/searchParams must be awaited"

**Cause:** Next.js 16 made request APIs async

**Solution:**

1. Add `async` to component function
2. Await `params` and `searchParams`
3. Use upgrade tool to apply codemods automatically

**Example Fix:**

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

### Module Not Found

**Error:** "Module not found: Can't resolve 'X'"

**Cause:** Missing dependency or incorrect import path

**Solution:**

1. Check if package is installed: `npm list <package>`
2. Install if missing: `npm install <package>`
3. Verify import path is correct
4. Check for typos in import statement

### Server Action Not Found

**Error:** "Server Action not found"

**Cause:** Server Action ID mismatch or missing function

**Solution:**

1. Use `get_server_action_by_id` to locate the action:

```json
{
  "action": "call_tool",
  "port": 3000,
  "toolName": "get_server_action_by_id",
  "args": {
    "id": "action-id-here"
  }
}
```

2. Verify function exists in source file
3. Check `'use server'` directive is present
4. Restart dev server to refresh action registry

## Best Practices for Debugging

### 1. Fix Errors in Order

1. Build errors (blocks everything)
2. TypeScript errors (prevents type safety)
3. Runtime errors (affects functionality)
4. Warnings (improves code quality)

### 2. Use Specific Queries

Instead of "show all errors", ask for:

- Errors on specific pages
- Specific error types (build, runtime, TypeScript)
- Errors in specific files or directories

### 3. Verify Fixes

After fixing errors:

1. Check errors again: `get_errors`
2. Test in browser: `browser_eval`
3. Review logs: `get_logs`

### 4. Leverage Documentation

Use `nextjs_docs` to search for:

- Error messages
- API documentation
- Best practices
- Migration guides

Example:

```
Next Devtools, search docs for "hydration mismatch"
```

### 5. Combine Tools

For complex debugging:

1. Get errors: `get_errors`
2. Get page context: `get_page_metadata`
3. Check logs: `get_logs`
4. Search docs: `nextjs_docs`
5. Test fix: `browser_eval`

## Troubleshooting the Debugging Process

### No Errors Returned

**Possible causes:**

- Dev server not running
- Wrong port specified
- Application has no errors

**Solutions:**

1. Verify server is running: `npm run dev`
2. Check correct port: `nextjs_index`
3. Manually check browser for errors

### Stale Error Information

**Cause:** Errors cached from previous build

**Solution:**

1. Save files to trigger rebuild
2. Restart dev server
3. Clear `.next` cache: `rm -rf .next`

### TypeScript Errors Not Showing

**Cause:** Type checking disabled

**Solution:**
Check `next.config.js`:

```javascript
module.exports = {
  typescript: {
    ignoreBuildErrors: false, // Should be false
  },
};
```

## Advanced Debugging Techniques

### Multi-Server Debugging

If running multiple Next.js projects:

1. List all servers: `nextjs_index`
2. Specify port for each query:

```json
{
  "action": "call_tool",
  "port": 3000,
  "toolName": "get_errors"
}
```

### Error Filtering

Focus on specific error types by analyzing the returned error structure:

- Filter by file path
- Filter by error severity
- Filter by error type (build/runtime/TypeScript)

### Continuous Monitoring

For long debugging sessions:

1. Get initial errors
2. Make fixes
3. Re-check errors
4. Repeat until clean

### Integration with Browser Testing

Combine runtime diagnostics with browser automation:

1. Get errors: `nextjs_call` with `get_errors`
2. Navigate to page: `browser_eval` with `navigate`
3. Check console: `browser_eval` with `console_messages`
4. Take screenshot: `browser_eval` with `screenshot`

This provides both server-side and client-side error information.

## Summary

The Next.js DevTools debugging workflow:

1. Initialize context (`init`)
2. Discover servers (`nextjs_index`)
3. Get errors (`nextjs_call` with `get_errors`)
4. Analyze and fix errors
5. Verify fixes
6. Use additional tools as needed (`get_page_metadata`, `get_logs`, `nextjs_docs`)

Always prioritize build errors, then TypeScript errors, then runtime errors for the most efficient debugging process.
ile paths and line numbers in error messages

#### Runtime Errors

- Occur d
