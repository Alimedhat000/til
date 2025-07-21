Instead of **polluting `App.tsx`** or your root component with many nested context providers (`<QueryClientProvider>`, `<HelmetProvider>`, `<ErrorBoundary>`, etc.), Bulletproof React introduces a cleaner pattern:

#### ✅ Wrap all global providers inside a single `AppProvider` component

```tsx
// App.tsx 
<AppProvider>
   <AppRoutes /> 
</AppProvider>
```

#### 🔁 Then define all contexts cleanly in one place:

```tsx
// AppProvider.tsx
<QueryClientProvider>
	<HelmetProvider>
        <ErrorBoundary>
			<AuthLoader>
				{children}
			</AuthLoader>
		</ErrorBoundary>
	</HelmetProvider>
</QueryClientProvider>`
```