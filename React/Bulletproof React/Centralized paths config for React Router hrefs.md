Instead of hardcoding URLs throughout the app, we can define a centralized `paths` object that: Stores all route `path` definitions for React Router,  Provides helper methods like `getHref()` to dynamically generate URLs, and  Improves consistency, refactorability, and prevents duplication

In the context of authentication, the `redirectTo` query parameter is used to **store the intended destination** a user was trying to reach **before** being redirected to the login or register page.

```ts
export const paths = {
  auth: {
    login: {
      path: '/auth/login',
      getHref: (redirectTo?: string) =>
        `/auth/login${redirectTo ? `?redirectTo=${encodeURIComponent(redirectTo)}` : ''}`,
    },
  },
  app: {
    discussion: {
      path: 'discussions/:discussionId',
      getHref: (id: string) => `/app/discussions/${id}`,
    },
  },
} as const;

```

Usage:

```tsx

const {navigate} = useNavigate();
navigate(paths.app.dashboard.getHref());

```

```tsx
<Link to={paths.auth.login.getHref('/app')}>Login</Link>
<Link to={paths.app.discussion.getHref('123')}>Discussion</Link>
```