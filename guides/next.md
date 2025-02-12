# NextJS

We will be focusing only on the `app` router, as it is more recent and recommended for new applications.

## Project structure
Important folders for setup:
* app - root of the source code for the application
* public - static assets to serve

Important files for routing:
* `layout.tsx` - layout for this directory and all child directories
* `page.tsx` - page content for this specific directory
* `loading.tsx`
* `not-found.tsx`
* `error.tsx`
* there are more, but focusing on these for now

### Routing in this context
Routing is done via directory structure.

A structure such as following:
```
--- page.tsx
 |- profile
      |- page.tsx
 |- articles
      |- [arcticleId]
            |- page.tsx
```
Indicates these routes:
* `/` root
* `/profile`
* `/articles/[articleId]`

Prefix a directory with `_` to opt out of routing.

### Organization
Next is generally unopinionated apart from the above.

Things to note:
* No route is publicly accessible until a page.tsx file is added to the directory
* No other files in that given directory are reachable from the client
* Private folder (starting with `_`) are ignored. THis can be good for:
    * Separating UI logic from routing logic
    * Consistent grouping of internal fiels
    * Avoiding naming conflicts

If a directory name is within parentheses, it will not be part of the route, and instead will be only for organization.

#### Some common structure choices
* Keep the `app` folder just for routing. All other related code lives outside.
* Store those project files in the `app` root (not outside)
* Localize route-specific files to the route folder


## Layouts + Pages

Create a page by placing the `page.tsx` file in the route directory. Whatever jsx that returns will be served at that specific route.

Layouts wrap any number of pages - they must render the `children` prop. For example:
```tsx
export default function HomeLayout({ children }: { children: React.ReactNode }) {
    return (
        <main>
            <h1>Home</h1>
            {children}
        </main>
    )
}
```

A layout in the `/app` directory is called a "root layout" and is required. It must contain `<html>` and `<body>` tags.

### Nested Layouts

By default layouts are nested the same way pages are. For example, if our `/home` directory has a layout, then the `page.tsx` in that directory will render inside the root layout and the home layout.

To get a route parameter that was defined via directory name (i.e. `/articles/[articleId]`), within that directory containing the square brackets you can use the following:
```tsx
export default async function Page({ params }: { params: Promise<{ articleId: string }>}) {
    const id = (await params).articleId;
    return (<article><h2>{id}</h2></article>);
}
```
### Linking
Use the default export from `next/link`:
```tsx
import Link from 'next/link'
```
And use like a regular anchor tag:
```tsx
<Link href={`/articles/${id}/metadata`}>See article metadata</Link>
```
Stick with `Link` unless you really need to use the hook, in which case `useRouter` exists.

## Images + Fonts
Recall we can store our static assets in the `public` folder.

### Images
Next provides an `<Image>` component which gives some benefits over the plain `<img>` tag.

1. Automatically serves proper size image for device
2. Prevents layout shift during loading
3. Faster loading by lazily loading images not in viewport
4. Images store on remote can be resized dynamically

Use the same way you would `img` in terms of `src` and `alt` attributes. Source can reference local or remote content.

For a local image, import it as a default import:
```tsx
import articleImage from './art.png';
```
And reference that import in `src`:
```tsx
<Image src={articleImage} {/* other props */} />
```
Local images don't require width and height props as these can be inferred. However, remote images do need these to prevent skew. The remote host must be allowed in `next.config.js`.

### Fonts
Next optimizes and automatically self-hosts fonts. All google fonts are accessible via `next/font/google` to use like so:
```tsx
import { Roboto } from 'next/font/google';

const roboto = Roboto({
    subsets: ['latin']
});
/** ... **/

return (
    <html lang="en" className={roboto.classname}>
        {/*...*/}
    </html>
)
```
Variables fonts are recommended by the Next docs. Local font usage is similar to the above:
```tsx
import NunitoSans from 'next/font/local'
 
const myFont = NunitoSans({
  src: './nunito-sans-regular.woff2',
})
```

## CSS

...gonna come back to this one. more important topics call...

## Fetching Data

Fetch data in a server component (any component marked as `async`) is as easy as awaiting at top level, using fetch api or some ORM/db.

On a client component, you can use the `use` hook or other libraries designed for fetching data. `use` can be leveraged to stream data. 

One way to fetch - on the server-side:
```tsx
import Posts from '@/app/ui/posts
import { Suspense } from 'react'
 
export default function Page() {
  // Don't await the data fetching function
  const posts = getPosts()
 
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Posts posts={posts} />
    </Suspense>
  )
}
```
On the client side:
```tsx
'use client'
import { use } from 'react'
 
export default function Posts({
  posts,
}: {
  posts: Promise<{ id: string; title: string }[]>
}) {
  const allPosts = use(posts)
 
  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```
The `<Suspense>` boundary is essential as we can use it define what is shown while the data is on the way.

### Streaming
To stream, the `dynamicIO` config option must be enabled.

With a `loading.tsx` file for the route, the whole page will be streamed and the user will see that loading state until the page can be rendered.

Explicitly using `Suspense` allows finer-grained control.

## Updating Data
Data can be updated by leverage React Server Functions.

In some `.ts` file, the line `'use server'` at the top indicates a Server Function. This can be inline if needed (frowned upon).
