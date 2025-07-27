`react-helmet-async` is a React library used to **manage the `<head>` of your HTML document** in a **declarative and async-friendly** way.

It's the async-capable, SSR-safe alternative to the now outdated `react-helmet` library.

It lets you update things like:

- `<title>`
- `<meta>` tags (for SEO, description, OpenGraph, Twitter cards)
- `<link rel="canonical">`
- And other `<head>` elements

From **within your React components** — instead of manipulating `document.head` manually.


Wrap Your App with `HelmetProvider`

```tsx
import { HelmetProvider } from 'react-helmet-async';

<HelmetProvider>
  <App />
</HelmetProvider>

```

then use 

```tsx
import { Helmet } from 'react-helmet-async';

function AboutPage() {
  return (
    <>
      <Helmet>
        <title>About - MyApp</title>
        <meta name="description" content="About page for MyApp" />
      </Helmet>
      <h1>About Us</h1>
    </>
  );
}

```

or make a head utility component 

```tsx 
import { Helmet, HelmetData } from 'react-helmet-async';

type HeadProps = {
  title?: string;
  description?: string;
};

const helmetData = new HelmetData({});

export const Head = ({ title = '', description = '' }: HeadProps = {}) => {
  return (
    <Helmet
      helmetData={helmetData}
      title={title ? `${title} | Bulletproof React` : undefined}
      defaultTitle="Bulletproof React"
    >
      <meta name="description" content={description} />
    </Helmet>
  );
};
```