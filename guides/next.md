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