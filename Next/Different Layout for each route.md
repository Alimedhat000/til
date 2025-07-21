Even though routes inside `(marketing)` and `(shop)` may share the same URL hierarchy (e.g. `/products`, `/about`), you can assign **different layouts** to each route group by placing a dedicated `layout.js` (or `layout.tsx`) file inside each folder. This is especially useful when you want sections of your app to have unique layouts, navigation bars, or sidebars without affecting other parts of the site.

```tsx
/app
  /(marketing)
    layout.tsx       → used only for marketing pages
    about/page.tsx   → /about
  /(shop)
    layout.tsx       → used only for shop pages
    products/page.tsx → /products

```

Each `layout.tsx` will wrap only the routes within its group, allowing you to maintain a clean separation of concerns and UI logic across different parts of your app.

This approach leverages **route groups**, which are folders wrapped in parentheses. These folders don’t affect the URL structure but allow you to organize your app and assign unique behavior or layout to each group.