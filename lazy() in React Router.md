React Router’s `lazy()` function enables **code-splitting** by allowing routes to be **dynamically loaded** only when they are needed (i.e., when the user navigates to them). This improves performance by reducing the initial JavaScript bundle size and speeding up the app’s load time.

Without `lazy()`, all route components are included in the initial bundle, even if some of them are never visited — which leads to unnecessary loading.

### Syntax
```ts
{
  path: '/about',
  lazy: () => import('./routes/about').then(m => ({ Component: m.default }))
}
```

>🔍 `lazy` returns a Promise that resolves to an object containing a `Component` key. This allows React Router to load the component asynchronously when the route is matched.
