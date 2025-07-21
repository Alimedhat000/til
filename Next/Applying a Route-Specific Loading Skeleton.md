To apply a [loading skeleton](https://nextjs.org/docs/app/api-reference/file-conventions/loading) using a `loading.tsx` file for a specific route only, you can use a **route group**. For example, to show a loading state **only on the `dashboard/overview` page**, create a new route group like `/(overview)` and move the `loading.tsx` file into that folder:

```tsx
/app
  /dashboard
    /(overview)
      loading.tsx      → applies only to /dashboard/overview
      overview/page.tsx

```

This ensures that the loading skeleton is scoped **only** to the `overview` page and **not shared** across other routes in the `dashboard` section — all while keeping the **URL path unchanged**.

`loading.tsx` will trigger automatically when navigating to that route if it's **async** (e.g., using `await`, `fetch()`, or `suspense` boundaries).