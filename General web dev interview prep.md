## General web dev interview prep

### Basic concepts

#### When is Server Side Rendering (SSR) better? When is Client-Side Rendering (CSR) better? What is the difference between the two?

CSR renders a website's JavaScript in the browser. The server sends only bare-bones HTML contain the JS files. This can mean that initial load is slow, but subsequent performace can be very fast.

In CSR, the following is the flow when a user accesses a page via the browser:

1. User enters URL into the browser. This sends a request to the server at that URL.
2. On the first request for a site, static files (CSS and HTML) are delivered to the browser.
3. Browser downloads the HTML first, then JavaScript. The HTML files connect the JavaScript.
4. After the JS is downloaded, content is dynamically generated in the browser.

SSR sends a fully rendered HTML page to the browser. For each new page, the process of fetching a receiving the page is repeated.

In SSR, the following is the flow when a user accesses a page via the browser:

1. User enters URL into the browser.
2. The server returns a fully rendered HTML page.
3. The browser render the page and downloads JS.

There is also Server Side Generation (SSG), in which the server sends static HTML files for each page, but also includes client-side JS that can be used to update the page as needed.

##### Differences

- CSR displays an empty page before loading. SSR displays fully rendered page on first loaded. Because of this loading difference, search engines can crawl the site better for better SEO
- CSR is cheaper, as it relieves the load on servers.
- CSR offers rich site interactions by providing fast interaction after the initial load. Fewer HTTP requests; slower transition between pages.
- SSR and SSG can be good for users with slower internet connections.
- SSG can be a little more complicated to set up and maintain.

Use SSR for content-heavy websites, or for better SEO optimization.
Use CSR for web applications, SPAs
Use SSG for websites that require both fast initial load times and dynamic updates, as well as SEO optimization
