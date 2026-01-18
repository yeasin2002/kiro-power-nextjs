# Upgrading to Next.js 16

This steering file provides comprehensive guidance for upgrading Next.js applications to version 16 using the automated upgrade tool.

## Overview

The `upgrade_nextjs_16` tool automates the upgrade process by:

- Analyzing your current Next.js version
- Running official Next.js codemods
- Updating dependencies in package.json
- Handling breaking changes automatically
- Providing guidance for manual changes

## Prerequisites

- Existing Next.js project (any version)
- Git repository (recommended for easy rollback)
- Node.js 16+ installed
- Clean working directory (commit or stash changes)

## Step-by-Step Upgrade Process

### 1. Backup Your Project

Before upgrading, ensure you have a backup:

```bash
# Commit all changes
git add .
git commit -m "Pre-upgrade checkpoint"

# Or create a backup branch
git checkout -b pre-nextjs-16-upgrade
git checkout main
```

### 2. Run the Upgrade Tool

Use the `upgrade_nextjs_16` tool:

```
Next Devtools, help me upgrade my Next.js app to version 16
```

The tool will:

1. Detect current Next.js version
2. Identify required changes
3. Run codemods automatically
4. Update package.json dependencies

### 3. Review Automated Changes

The upgrade tool applies these codemods:

#### Async Request APIs Codemod

Converts synchronous request APIs to async:

**Before:**

```typescript
export default function Page({ params, searchParams }) {
  return <div>{params.id}</div>
}
```

**After:**

```typescript
export default async function Page({ params, searchParams }) {
  const resolvedParams = await params
  const resolvedSearchParams = await searchParams
  return <div>{resolvedParams.id}</div>
}
```

#### Async Dynamic APIs Codemod

Updates `cookies()`, `headers()`, and `draftMode()`:

**Before:**

```typescript
import { cookies } from "next/headers";

export function getUser() {
  const token = cookies().get("token");
  return token;
}
```

**After:**

```typescript
import { cookies } from "next/headers";

export async function getUser() {
  const cookieStore = await cookies();
  const token = cookieStore.get("token");
  return token;
}
```

### 4. Install Updated Dependencies

After codemods complete:

```bash
# Remove old dependencies
rm -rf node_modules package-lock.json

# Install new dependencies
npm install

# Or with yarn
yarn install

# Or with pnpm
pnpm install
```

### 5. Start Development Server

Test the upgrade:

```bash
npm run dev
```

Watch for:

- Build errors
- Runtime errors
- TypeScript errors
- Deprecation warnings

### 6. Check for Errors

Use Next.js DevTools to identify issues:

```
Next Devtools, what errors are in my application?
```

The tool will use `get_errors` to show:

- Build errors from upgrade
- TypeScript errors from API changes
- Runtime errors from breaking changes

## Breaking Changes in Next.js 16

### 1. Async Request APIs

**What changed:** `params`, `searchParams`, `cookies()`, `headers()`, and `draftMode()` are now async

**Impact:** All components and functions using these APIs must be updated

**Automated:** Yes, codemods handle this

**Manual check needed:**

- Verify all usages are converted
- Check for indirect usages (passed as props)
- Test dynamic routes thoroughly

### 2. Fetch Cache Default

**What changed:** `fetch()` no longer caches by default

**Impact:** May affect performance if you relied on automatic caching

**Automated:** No

**Manual action:**

```typescript
// Explicitly opt into caching
fetch("https://api.example.com/data", {
  cache: "force-cache",
});

// Or use Next.js cache
import { cache } from "react";

const getData = cache(async () => {
  return fetch("https://api.example.com/data");
});
```

### 3. Minimum React Version

**What changed:** Requires React 19+

**Impact:** Must upgrade React alongside Next.js

**Automated:** Yes, package.json is updated

**Manual check:**

- Verify React 19 compatibility of dependencies
- Test React 19 features if used
- Update React types if using TypeScript

### 4. Removed APIs

**What changed:** Some deprecated APIs removed

**Impact:** Code using old APIs will break

**Automated:** Partially

**Common removals:**

- `next/image` legacy props
- Old middleware API patterns
- Deprecated config options

## Post-Upgrade Checklist

### 1. Build Verification

```bash
# Test production build
npm run build

# Check for build errors
# Check for warnings
# Verify output size
```

### 2. Type Checking

```bash
# Run TypeScript compiler
npx tsc --noEmit

# Fix any type errors
# Update type definitions if needed
```

### 3. Test Suite

```bash
# Run all tests
npm test

# Run E2E tests
npm run test:e2e

# Fix failing tests
```

### 4. Feature Testing

Manually test:

- [ ] All routes load correctly
- [ ] Dynamic routes work with new async params
- [ ] API routes function properly
- [ ] Authentication flows work
- [ ] Data fetching behaves as expected
- [ ] Forms submit correctly
- [ ] Client-side navigation works
- [ ] Server Actions execute properly

### 5. Performance Check

Compare before and after:

- Page load times
- Build times
- Bundle sizes
- Lighthouse scores

### 6. Browser Testing

Test in multiple browsers:

- Chrome
- Firefox
- Safari
- Edge

## Common Upgrade Issues

### Issue: "Cannot read properties of undefined"

**Cause:** Forgot to await async APIs

**Solution:**

```typescript
// Wrong
const { id } = params; // params is a Promise

// Correct
const { id } = await params;
```

### Issue: Build fails with "Module not found"

**Cause:** Dependency version incompatibility

**Solution:**

```bash
# Clear cache and reinstall
rm -rf node_modules .next
npm install
```

### Issue: TypeScript errors after upgrade

**Cause:** Type definitions changed

**Solution:**

```bash
# Update TypeScript
npm install -D typescript@latest

# Update Next.js types
npm install -D @types/node@latest @types/react@latest
```

### Issue: "fetch is not defined" in tests

**Cause:** Test environment needs fetch polyfill

**Solution:**

```javascript
// jest.setup.js
import { fetch } from "undici";
global.fetch = fetch;
```

### Issue: Middleware not working

**Cause:** Middleware API changes

**Solution:**
Check middleware.ts for deprecated patterns:

```typescript
// Update to new middleware API
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  return NextResponse.next();
}
```

## Advanced Upgrade Scenarios

### Monorepo Upgrades

For monorepos with multiple Next.js apps:

1. Upgrade one app at a time
2. Test thoroughly before moving to next app
3. Share learnings across apps
4. Consider upgrading shared packages first

### Large Codebase Strategy

For large applications:

1. **Create feature branch:**

   ```bash
   git checkout -b upgrade-nextjs-16
   ```

2. **Run upgrade tool:**

   ```
   Next Devtools, upgrade my Next.js app to version 16
   ```

3. **Fix errors incrementally:**
   - Start with build errors
   - Then TypeScript errors
   - Then runtime errors
   - Finally warnings

4. **Test in stages:**
   - Unit tests
   - Integration tests
   - E2E tests
   - Manual testing

5. **Deploy to staging:**
   - Test in production-like environment
   - Monitor for issues
   - Performance testing

6. **Merge to main:**
   ```bash
   git checkout main
   git merge upgrade-nextjs-16
   ```

### Gradual Migration

If full upgrade is too risky:

1. Upgrade Next.js 16 but keep old patterns
2. Gradually adopt new features
3. Use feature flags for new code
4. Migrate page by page

## Using Cache Components After Upgrade

Once upgraded to Next.js 16, consider enabling Cache Components:

```
Next Devtools, enable Cache Components in my Next.js app
```

This provides:

- Better caching control
- Improved performance
- Granular cache invalidation

See the `cache-components.md` steering file for details.

## Rollback Procedure

If upgrade causes critical issues:

### 1. Revert Code Changes

```bash
# If using git
git reset --hard HEAD~1

# Or restore from backup branch
git checkout pre-nextjs-16-upgrade
```

### 2. Restore Dependencies

```bash
# Reinstall old dependencies
rm -rf node_modules package-lock.json
npm install
```

### 3. Clear Build Cache

```bash
rm -rf .next
npm run dev
```

### 4. Document Issues

Before rolling back, document:

- What broke
- Error messages
- Steps to reproduce
- Attempted fixes

This helps for future upgrade attempts.

## Getting Help

If you encounter issues during upgrade:

### 1. Search Documentation

```
Next Devtools, search docs for [your issue]
```

### 2. Check Error Messages

```
Next Devtools, what errors are in my application?
```

### 3. Review Upgrade Guide

Official Next.js 16 upgrade guide:
https://nextjs.org/docs/app/building-your-application/upgrading/version-16

### 4. Community Resources

- Next.js GitHub Discussions
- Next.js Discord
- Stack Overflow

## Summary

Successful Next.js 16 upgrade process:

1. **Prepare:** Backup code, commit changes
2. **Upgrade:** Run `upgrade_nextjs_16` tool
3. **Install:** Update dependencies with `npm install`
4. **Test:** Check for errors with `get_errors`
5. **Fix:** Address any issues found
6. **Verify:** Run tests and manual testing
7. **Deploy:** Ship to production

The automated upgrade tool handles most changes, but always review and test thoroughly before deploying to production.
