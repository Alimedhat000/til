
```tsx
<React.Suspense
  fallback={
    <div className="flex h-screen w-screen items-center justify-center">
      <Spinner size="xl" />
    </div>
  }
>
  {/* Lazy-loaded content */}
</React.Suspense>

```

**`React.Suspense`** is used to **wrap components that are loaded lazily** (e.g., via `React.lazy()` or dynamic imports), The **`fallback`** prop defines what gets rendered while those components are still loading. 

In this case, it's a **full-page centered loading spinner**, giving users immediate feedback that something is loading.