The common example is a `posts` route that includes a _slug_ to dynamically reference a particular post. The template for that page can be defined at `pages/posts/[slug].js`. Notice the square brackets around the slug, that tells Next that it is a dynamic route and whatever matches against the slug should be included in `router.query` as `slug`.

```bash
$ touch pages/posts/\[slug\].js
```

#### in a SSR component 

```tsx
export default async function Page({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  return <div>My Post: {slug}</div>
}
```


#### in Client Components

In a Client Component **page**, dynamic segments from props can be accessed using the [`use`](https://react.dev/reference/react/use) hook.

```tsx
'use client'
import { use } from 'react'
 
export default function BlogPostPage({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = use(params)
 
  return (
    <div>
      <p>{slug}</p>
    </div>
  )
}
```


## [Catch-all Segments](https://nextjs.org/docs/app/api-reference/file-conventions/dynamic-routes#catch-all-segments)

Dynamic Segments can be extended to **catch-all** subsequent segments by adding an ellipsis inside the brackets `[...folderName]`. For example, `app/shop/[...slug]/page.js` will match `/shop/clothes`, but also `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`, and so on.

Catch-all Segments can be made **optional** by including the parameter in double square brackets: `[[...folderName]]`. For example, `app/shop/[[...slug]]/page.js` will **also** match `/shop`, in addition to `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`.

The difference between **catch-all** and **optional catch-all** segments is that with optional, the route without the parameter is also matched (`/shop` in the example above).


## Typescript 

When using TypeScript, you can add types for `params` depending on your configured route segment.

|Route|`params` Type Definition|
|---|---|
|`app/blog/[slug]/page.js`|`{ slug: string }`|
|`app/shop/[...slug]/page.js`|`{ slug: string[] }`|
|`app/shop/[[...slug]]/page.js`|`{ slug?: string[] }`|
|`app/[categoryId]/[itemId]/page.js`|`{ categoryId: string, itemId: string }`|