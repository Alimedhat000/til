### `ErrorBoundary` component

Wrap an `ErrorBoundary` component around other React components to "catch" errors and render a fallback UI. The component supports several ways to render a fallback (as shown below).

```tsx 
import { ErrorBoundary } from "react-error-boundary";

function Fallback({ error, resetErrorBoundary }) {
  // Call resetErrorBoundary() to reset the error boundary and retry the render.

  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre style={{ color: "red" }}>{error.message}</pre>
    </div>
  );
}

<ErrorBoundary
  FallbackComponent={Fallback}
  onReset={(details) => {
    // Reset the state of your app so the error doesn't happen again
  }}
>
  <ExampleApplication />
</ErrorBoundary>;
```

#### Logging errors with `onError`

```tsx 
import { ErrorBoundary } from "react-error-boundary";

const logError = (error: Error, info: { componentStack: string }) => {
  // Do something with the error, e.g. log to an external API
};

const ui = (
  <ErrorBoundary FallbackComponent={ErrorFallback} onError={logError}>
    <ExampleApplication />
  </ErrorBoundary>
);
```