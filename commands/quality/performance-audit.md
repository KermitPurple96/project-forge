# 🤖 Command: performance-audit

> Identifies performance bottlenecks in the codebase.

## When to use

Before production launch. When users report slowness. After adding significant features.

## Instructions for Claude

1. **Database:**
   - N+1 query patterns
   - Missing indexes for frequent queries
   - Large unbounded queries (no LIMIT/pagination)
   - Expensive JOINs
2. **Frontend:**
   - Bundle size analysis (if applicable)
   - Unnecessary re-renders (React: missing memo, unstable references)
   - Large images without optimization/lazy loading
   - Missing code splitting
   - Layout shifts (CLS)
3. **Backend:**
   - Synchronous operations that should be async
   - Missing caching for expensive operations
   - Memory leaks (event listeners, growing arrays, unclosed connections)
   - Inefficient algorithms (O(n²) where O(n) is possible)
4. **API:**
   - Over-fetching (returning full objects when only IDs needed)
   - Missing compression (gzip/brotli)
   - No pagination on list endpoints
5. Generate report with prioritized fixes and estimated impact
