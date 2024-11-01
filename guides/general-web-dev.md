## General web dev interview prep

### Basic concepts

#### What is a web server?

This could be hardware, software, or the two together. When referring to hardware, a web server is a computer that stores software and website components. This hardware connects to the Internet and supports data interchange with other conencted devices. When referring to hardware, a web server is a program controller how users access hosted files. At a minimum, the server responds to HTTP requests and understands URLs, delivering content to client devices based on the requests.

#### What is the difference between a static web server and a dynamic web server?

A static web server sends its hosted files as-is to the browser.

A dynamic web server consists of a static web server plus extra software, usually an application server and a database. It is dynamic because the hosted files are updated prior to their sending to the browser.

#### What is an API?

An Application Programming Interfacing is a definition of rules to follow to communicate with other software systems - a gateway between clients and resources on the web.

#### What is a RESTful API?

REST (Respresentational State Transfer) imposes conditions on how an API should work. REST-based architecture is designed to support high-performing and reliable communication at scale.

Principles of REST:

- Uniform interface - server must transfer information in a standard format. This comes with four architectural constraints:
  1. Requests should identify resources via a uniform resource identifier
  2. Clients have enough information to modify or delete resources. Servers provide metadata with responses to describe the resources.
  3. Clients receive information about how to further process resources.
  4. Clients receive information about related resources to complete whatever task
- Statelessness - all requests are completed independently of the previous ones
- Layered system - the system can work with intermediaries between client and server and/or with several layers of system behind that which is exposed to the client. These all work together to RESTfully fulfill client requests.
- Cache-ability - REST APIs support caching by sending information in responses about how to cache resources
- Code on demand (optional) - servers can temporarily extend or customize client functionality by transferring software programming to the client (such as JS to run client-side)

##### Benefits of using REST

- Scalability - statelessness removes server load to optimize client-server interactions. Well-managed caching works towards this goal as well.
- Flexibility - client and server are decoupled such that each part can evolve independently. Platform or technology changes do not change the interface
- Independence - independent of technology used. Only the interface is uniform, behind-the-scenes computation is abstracted from the client. Similarly, the server doesn't care about the client's implementation.

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

#### What is a proxy server?

A server that sits in front of a group of client machines. When these client machines makes requests to sites and services on the Internet, the proxy intercepts those requests and communicates on behalf of the clients. These can be used to:

- Avoid browsing restrictions from firewalls by allowing clients to communicate with the proxy instead of directly with the restricted sites.
- Block access to certain content by imposing content filter rules via the proxy before returning responses to the client machine
- To protect online identiy by making it difficult to trace requests back to the machine of origin.

#### What is a reverse proxy?

A server that sits in front of web servers and forwards client requests to those web servers. Benefits of a reverse proxy:

- Load balancing to distribute traffic evenly among different servers
- Protection from attacks by not revealing servers' IP addresses. The proxy can have more resources in place to fight off the attack
- Reverse proxies can cache content to improve performance. Reverse proxies can be closer to the request machines to serve content more quickly.
- Can decrypt incoming requests and encrypt outgoing requests to free up server resources.

### HTML/CSS

HTML is a markup language that tells web browsers how to structure web pages you visit.

Reserved characters:

- `<` escaped as `&lt;`
- `>` escaped as `&gt;`
- `"` escaped as `&quot;`
- `'` escaped as `&apos;`
- `&` escaped as `&amp;`

Notes on some specific HTML tags:

- `<!DOCTYPE html>` - this is a required preamble. Used to mean more, these days it should just be added to ensure correct behavior
- `<html></html>` - must wrap all the content on the page, AKA the root element. also includes the `lang` attribute which sets the primary language of the document
- `<head></head>` - container for all things not visible by the user. can include things like keywords and a page description that you want to appear in search results
- `<meta charset="utf-8">` - sets the character set for the page
- `<meta name="viewport" content="width=device-width">` - ensure the page renders at the width of the viewport, preventing mobile browsers from rendering pages too wide and then auto-shrinking them
- `<body></body>` - contains all the user-viewable stuff
- `<link rel="stylesheet" href="my-css-file.css" />` - add a css stylesheet to the doc
- `<script src="my-js-file.js" defer></script>` - load a script

#### Sctructural hierarchy in HTML documents

The hierarchy of an HTML page is important for several reasons

- heading contents are considered important keywords for SEO
- headings are used to provide an outline of a page for those consuming the page via screen reader

Generally your document should:

- Use just a single `<h1>` per page
- Use headings in the correct order (i.e. an `<h2>` should not appear as a child of an `<h3>`)
- Of the six available heading levels, aim to use no more than three per page
- Make use of the layout elements detailed below

Layout elements:

- `<main>` is for content unique to the page. Use once, directly inside the body
- `<article>` encloses a block of related content that makes sense on its own without the rest of the page
- `<section>` similar to article but for grouping together a part of the page constituting one piece of functionality
- `<aside>` contains content with additional info but not necessarily related to the main content on the page
- `<header>` is used for a group of introductory content
- `<nav>` used to wrap the nav bar
- `<footer>` wraps the content at the page's bottom

---
