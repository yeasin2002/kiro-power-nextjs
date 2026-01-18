# Cache Components in Next.js 16

This steering file provides comprehensive guidance for enabling and using Cache Components in Next.js 16.

## Overview

Cache Components is a new caching paradigm in Next.js 16 that provides:

- Granular caching control with `'use cache'` directive
- Public and private cache modes
- Fine-grained cache invalidation
- Better performance for dynamic content

## Prerequisites

- Next.js 16+ installed
- Understanding of Server Components
- Familiarity with async/await patterns

## Enabling Cache Components

### Automated Setup

Use the `enable_cache_components` tool for automated setup:

```
Next Devtools, enable Cache Components in my Next.js app
```

The tool will:

1. Update `next.config.js` with `experimental.cacheComponents: true`
2. Start dev server and detect caching errors
3. Apply automated fixes:
   - Add Suspense boundaries
   - Add `'use cache'` directives
   - Configure static parameters for dynamic routes
4. Verify setup is error-free

### Manual Setup

If you prefer manual setup:

1. **Update next.config.js:**

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    cacheComponents: true,
  },
};

module.exports = nextConfig;
```

2. **Restart dev server:**

```bash
npm run dev
```

3. **Check for errors:**

```
Next Devtools, what errors are in my application?
```

## Cache Modes

### Public Cache (Default)

Public caches are shared across all users:

```typescript
"use cache";
export async function getPublicData() {
  const data = await fetch("https://api.example.com/public");
  return data.json();
}
```

**Use cases:**

- Blog posts
- Product listings
- Public API data
- Static content
- Marketing pages

**Benefits:**

- Maximum performance
- Reduced server load
- Lower API costs
- Faster response times

### Private Cache

Private caches are per-user:

```typescript
"use cache: private";
export async function getUserData() {
  const session = await getSession();
  const data = await fetch(`https://api.example.com/user/${session.id}`);
  return data.json();
}
```

**Use cases:**

- User profiles
- Personalized content
- Shopping carts
- User preferences
- Private dashboards

**Benefits:**

- User-specific caching
- Secure data isolation
- Personalized performance
- Reduced redundant fetches

## Cache Directive Placement

### Component Level

Cache entire component output:

```typescript
'use cache'
export async function ProductCard({ id }: { id: string }) {
  const product = await getProduct(id)

  return (
    <div>
      <h2>{product.name}</h2>
      <p>{product.price}</p>
    </div>
  )
}
```

### Function Level

Cache function results:

```typescript
'use cache'
async function getProducts() {
  return await db.products.findMany()
}

export async function ProductList() {
  const products = await getProducts()
  return <div>{/* render products */}</div>
}
```

### Multiple Cache Directives

Different cache modes in same file:

```typescript
"use cache";
async function getPublicProducts() {
  return await db.products.findMany({ where: { public: true } });
}

("use cache: private");
async function getUserProducts(userId: string) {
  return await db.products.findMany({ where: { userId } });
}
```

## Cache Invalidation

### Using updateTag

Invalidate specific cache entries:

```typescript
import { updateTag } from "next/cache";

// Tag cached data
("use cache");
export async function getProducts() {
  const products = await fetch("https://api.example.com/products", {
    next: { tags: ["products"] },
  });
  return products.json();
}

// Invalidate when data changes
export async function createProduct(data: ProductData) {
  await db.products.create({ data });

  // Invalidate products cache
  updateTag("products");
}
```

### Using revalidateTag

Revalidate and refetch:

```typescript
import { revalidateTag } from "next/cache";

export async function updateProduct(id: string, data: ProductData) {
  await db.products.update({ where: { id }, data });

  // Revalidate specific product
  revalidateTag(`product-${id}`);

  // Revalidate all products
  revalidateTag("products");
}
```

### Using revalidatePath

Revalidate entire routes:

```typescript
import { revalidatePath } from "next/cache";

export async function updateHomePage() {
  await db.settings.update({
    /* ... */
  });

  // Revalidate homepage
  revalidatePath("/");

  // Revalidate all blog posts
  revalidatePath("/blog", "page");
}
```

## Cache Configuration

### Cache Lifetime

Control how long data is cached:

```typescript
import { cacheLife } from "next/cache";

("use cache");
export async function getNews() {
  cacheLife("minutes");

  return await fetch("https://api.example.com/news");
}
```

**Available lifetimes:**

- `'seconds'` - Very short-lived data
- `'minutes'` - Frequently changing data
- `'hours'` - Moderately stable data
- `'days'` - Stable data
- `'weeks'` - Very stable data
- `'max'` - Maximum cache duration

### Custom Cache Duration

```typescript
"use cache";
export async function getData() {
  cacheLife({
    stale: 60, // Serve stale for 60 seconds
    revalidate: 300, // Revalidate after 5 minutes
    expire: 3600, // Expire after 1 hour
  });

  return await fetch("https://api.example.com/data");
}
```

### Cache Tags

Organize caches with tags:

```typescript
import { cacheTag } from "next/cache";

("use cache");
export async function getProduct(id: string) {
  cacheTag(`product-${id}`, "products", "catalog");

  return await db.products.findUnique({ where: { id } });
}

// Invalidate by any tag
updateTag("products"); // Invalidates all products
updateTag(`product-${id}`); // Invalidates specific product
updateTag("catalog"); // Invalidates entire catalog
```

## Common Patterns

### Pattern 1: Cached Data Fetching

```typescript
'use cache'
async function fetchUserPosts(userId: string) {
  cacheLife('hours')
  cacheTag(`user-${userId}-posts`, 'posts')

  return await db.posts.findMany({
    where: { userId },
    orderBy: { createdAt: 'desc' }
  })
}

export async function UserPosts({ userId }: { userId: string }) {
  const posts = await fetchUserPosts(userId)

  return (
    <div>
      {posts.map(post => (
        <PostCard key={post.id} post={post} />
      ))}
    </div>
  )
}
```

### Pattern 2: Incremental Static Regeneration

```typescript
'use cache'
export async function BlogPost({ slug }: { slug: string }) {
  cacheLife('days')
  cacheTag(`blog-${slug}`, 'blog')

  const post = await getPost(slug)

  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  )
}

// Revalidate when post is updated
export async function updatePost(slug: string, data: PostData) {
  await db.posts.update({ where: { slug }, data })
  updateTag(`blog-${slug}`)
}
```

### Pattern 3: User-Specific Caching

```typescript
'use cache: private'
async function getUserDashboard(userId: string) {
  cacheLife('minutes')

  const [profile, stats, activity] = await Promise.all([
    getUserProfile(userId),
    getUserStats(userId),
    getUserActivity(userId)
  ])

  return { profile, stats, activity }
}

export async function Dashboard() {
  const session = await getSession()
  const data = await getUserDashboard(session.userId)

  return <DashboardView data={data} />
}
```

### Pattern 4: Conditional Caching

```typescript
async function getProducts(filters: ProductFilters) {
  // Only cache if no filters applied
  if (Object.keys(filters).length === 0) {
    ("use cache");
    cacheLife("hours");
  }

  return await db.products.findMany({
    where: filters,
  });
}
```

### Pattern 5: Nested Caching

```typescript
"use cache";
async function getCategory(id: string) {
  cacheTag(`category-${id}`);
  return await db.categories.findUnique({ where: { id } });
}

("use cache");
async function getCategoryProducts(categoryId: string) {
  cacheTag(`category-${categoryId}-products`);

  const category = await getCategory(categoryId);
  const products = await db.products.findMany({
    where: { categoryId },
  });

  return { category, products };
}
```

## Dynamic Routes with Cache Components

### Static Parameters

For dynamic routes, define static parameters:

```typescript
// app/blog/[slug]/page.tsx
'use cache'
export async function generateStaticParams() {
  const posts = await db.posts.findMany()

  return posts.map(post => ({
    slug: post.slug
  }))
}

'use cache'
export default async function BlogPost({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params
  const post = await getPost(slug)

  return <article>{/* render post */}</article>
}
```

### Dynamic Parameters

For truly dynamic routes:

```typescript
// app/search/[query]/page.tsx
export default async function SearchResults({ params }: { params: Promise<{ query: string }> }) {
  const { query } = await params

  // Don't cache search results (too dynamic)
  const results = await searchProducts(query)

  return <SearchView results={results} />
}
```

## Suspense Boundaries

Cache Components often require Suspense boundaries:

```typescript
import { Suspense } from 'react'

export default function Page() {
  return (
    <div>
      <h1>My Page</h1>

      <Suspense fallback={<LoadingSkeleton />}>
        <CachedContent />
      </Suspense>

      <Suspense fallback={<LoadingSpinner />}>
        <AnotherCachedComponent />
      </Suspense>
    </div>
  )
}

'use cache'
async function CachedContent() {
  const data = await fetchData()
  return <div>{data}</div>
}
```

## Error Handling

### Common Cache Component Errors

#### Error: "Missing Suspense boundary"

**Cause:** Cached component not wrapped in Suspense

**Solution:**

```typescript
<Suspense fallback={<Loading />}>
  <CachedComponent />
</Suspense>
```

#### Error: "Cannot use 'use cache' in Client Component"

**Cause:** Cache directive in client component

**Solution:**
Remove `'use client'` or move caching to Server Component

#### Error: "Dynamic parameter not awaited"

**Cause:** Forgot to await params in cached component

**Solution:**

```typescript
const { id } = await params; // Must await
```

## Testing Cache Components

### Test Cache Behavior

```typescript
// __tests__/cached-component.test.tsx
import { render } from "@testing-library/react";
import { CachedComponent } from "./CachedComponent";

describe("CachedComponent", () => {
  it("renders cached data", async () => {
    const { getByText } = render(await CachedComponent({ id: "1" }));
    expect(getByText("Cached Content")).toBeInTheDocument();
  });
});
```

### Test Cache Invalidation

```typescript
import { updateTag } from "next/cache";

describe("Cache invalidation", () => {
  it("invalidates cache on update", async () => {
    await updateProduct("1", { name: "New Name" });

    // Verify cache was invalidated
    expect(updateTag).toHaveBeenCalledWith("product-1");
  });
});
```

## Performance Optimization

### Optimize Cache Granularity

**Too coarse:**

```typescript
"use cache";
async function getAllData() {
  // Caches everything together
  return {
    users: await getUsers(),
    posts: await getPosts(),
    comments: await getComments(),
  };
}
```

**Better:**

```typescript
"use cache";
async function getUsers() {
  cacheTag("users");
  return await db.users.findMany();
}

("use cache");
async function getPosts() {
  cacheTag("posts");
  return await db.posts.findMany();
}

// Invalidate independently
updateTag("users"); // Only invalidates users
updateTag("posts"); // Only invalidates posts
```

### Parallel Data Fetching

```typescript
"use cache";
async function getPageData() {
  const [header, content, sidebar] = await Promise.all([
    getHeader(),
    getContent(),
    getSidebar(),
  ]);

  return { header, content, sidebar };
}
```

## Best Practices

1. **Use public cache for shared data**
   - Blog posts, products, public APIs

2. **Use private cache for user data**
   - Profiles, preferences, personalized content

3. **Tag your caches**
   - Makes invalidation easier and more precise

4. **Set appropriate cache lifetimes**
   - Frequently changing: minutes
   - Stable: hours or days

5. **Wrap cached components in Suspense**
   - Provides loading states
   - Prevents blocking

6. **Invalidate caches on mutations**
   - Use updateTag after creates/updates/deletes

7. **Test cache behavior**
   - Verify caching works as expected
   - Test invalidation logic

8. **Monitor cache performance**
   - Check cache hit rates
   - Measure response times

## Troubleshooting

Use Next.js DevTools to debug cache issues:

```
Next Devtools, what errors are in my application?
```

Common issues:

- Missing Suspense boundaries
- Incorrect cache directives
- Dynamic params not awaited
- Cache not invalidating

## Summary

Cache Components in Next.js 16 provide powerful caching capabilities:

- **Public cache** for shared data
- **Private cache** for user-specific data
- **Granular invalidation** with tags
- **Flexible lifetimes** with cacheLife
- **Suspense integration** for loading states

Use the `enable_cache_components` tool for automated setup, then leverage these patterns for optimal performance.
