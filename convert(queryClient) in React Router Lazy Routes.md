When using `lazy()` in React Router, the loaded module may export a `clientLoader`, `clientAction`, or other route logic that needs access to shared dependencies like the `queryClient` from React Query.

### Why use `convert(queryClient)`?

The `convert()` function allows you to **inject shared dependencies (like `queryClient`) into each route’s loader and action at definition time**, rather than relying on global context or wiring everything up manually in a root-level provider.

This enables **per-route encapsulation** of data fetching and mutation logic — so each route remains self-contained, testable, and maintainable.

### Example
```ts
const convert = (queryClient: QueryClient) => (m: any) => {
  const { clientLoader, clientAction, default: Component, ...rest } = m;
  return {
    ...rest,
    loader: clientLoader?.(queryClient),
    action: clientAction?.(queryClient),
    Component,
  };
};
```

and then when using [[lazy() in React Router]] you can just do this 

```tsx
    {
      path: paths.home.path,
      lazy: () => import('./routes/landing').then(convert(queryClient)),
    },
```

With this pattern, every route can stay **focused and independently responsible for its own data**, making your application **more scalable, performant, and maintainable** over time.

### Why this is life-changing
- **Cleaner Architecture:** Routes can define their own data needs without polluting global state or requiring awkward prop drilling/context sharing.

-  **Better Performance:** Works seamlessly with lazy() and code-splitting — only the necessary code and data logic are loaded when the route is hit.

-  **Improved Reusability:** You can reuse loaders/actions across different apps or setups just by passing the relevant shared dependencies.

-  **Testability:** Logic stays functional and testable — no need to mock global providers in tests.

-  **Plug-and-play Route Modules:** Imagine building your routes as truly modular files that work out of the box when dropped into any React Router setup with a shared dependency injection mechanism.