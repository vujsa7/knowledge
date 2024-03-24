# What is Svelte and what is SvelteKit?
---
Svelte and SvelteKit are both frameworks used for building web applications but they have different purposes.
- Svelte - it's a framework for building UIs and if used alone server for building SPAs (single page applications) just like React, Angular and Vue.
- SvelteKit - on the other hand is built on top of Svelte and adds support for building full-stack applications. It provides features for routing, SSR (server side rendering) and more out of the box 

> [!tip]+ What to choose? Svelte or SvelteKit?
>If you need to build a frontend app you can use Svelte, but if you need to use a database or something that should not be on the client, use SvelteKit. Also it's super easy to go from using Svelte to using SvelteKit.

Svelte comes with its own compiler and build tools, providing better development experience. Just by plugging in the svelte router you can use svelte file system routing without the need to use SvelteKit.

# Why choose Svelte over React, Angular and Vue?
---
Even if you're building a small SPA choosing Svelte over traditional SPA libraries and frameworks such as React, Angular and Vue offers several benefits:
- **Smaller bundle size and performance:** Svelte compiles your code to highly optimized vanilla JavaScript during the build process. This results in smaller bundle sizes and more efficient runtime performance compared to traditional SPAs, which often require a larger runtime library and additional overhead.
- **Developer experience:** Svelte syntax is clean and easy to understand. It provides reactivity out of the box and there's no need to manage the states within components. This makes the code much more concise and easier to maintain. Svelte's compilation step catches errors early in the development process, reducing the likelihood of runtime errors and making debugging easier.
- **Learning curve**: Smaller API surface compared to traditional SPA frameworks, makes Svelte quicker to learn and easier to master, especially for developers new to frontend development. No Virtual DOM concepts and JSX syntax also make Svelte more friendlier to newcomers. Svelte comes with its own compiler and build tools, providing a cohesive and integrated development experience. This reduces the need for additional tooling and configuration compared to traditional SPAs, which often require setting up bundlers, transpilers, and other build tools separately.
- **Extensibility**: You have the option to extend your application, and make it become more than just a SPA. You can build both frontend and full-stack applications. Whether you want to build a RESTful API with Node.js, use serverless functions with platforms like AWS Lambda or Firebase, or implement a GraphQL server, you can seamlessly integrate Svelte with your chosen backend technology.
- **Community and Ecosystem**: Svelte has a growing community and ecosystem, with various libraries, tools, and resources. Whether you need database integration, authentication, or other backend-related functionalities, you can find community-maintained packages and solutions to help you achieve your goals.

## How is SvelteKit different from building frontend apps the way we're used to (SPAs)?

You can do a lot of different stuff with SvelteKit, but it's primarily used if you are building a web application with UI. With it, you can:
- Build a client side app without SSR, that will be consuming a third party API (you want Svelte)
- Build an app with SSR where your backend will be consuming a third party API and/or your backend has it's own API endpoints and a database, etc... (you want SvelteKit)
However if you're building a backend system that should support microservice architecture, SvelteKit may not be the best tool for the job.

# How to create a project?
---
## Svelte for SPAs

We're going to use the [Svelte + Vite](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-svelte) template. You don't have to use a project template, but it means you have to do a lot less setup work.

```bash
npm create vite@latest my-svelte-project -- --template svelte-ts
```

You can check out the project [documentation](https://svelte.dev/blog/svelte-for-new-developers#creating-a-project) for more in depth tutorial and explanations on what happens under the hood when creating a project. 

## SvelteKit for full-stack apps

The easiest way to start building a SvelteKit app is to run `npm create`:

```bash
npm create svelte@latest my-app
```

Checkout the [documentation](https://kit.svelte.dev/docs/creating-a-project) for further instructions.

Project Structure
---
Let's dive deep into how your project structure may look and what goes where. This is a typical SvelteKit project structure mentioned in Svelte documentation, but with slight modifications according to my own preferences.

```
my-project/
├ src/
│ ├ lib/
│ │ ├ server/
│ │ │ └ [your server-only lib files]
│ │ └ client/
│ │   └ [your client-only lib files]
│ ├ params/
│ │ └ [your param matchers]
│ ├ routes/
│ │ ├ [your routes]
│ │ └ api/
│ │   └ [your server api endpoints]
│ ├ app.html
│ ├ error.html
│ ├ hooks.client.js
│ ├ hooks.server.js
│ └ service-worker.js
├ static/
│ └ [your static assets]
├ tests/
│ └ [your tests]
├ package.json
├ svelte.config.js
├ tsconfig.json
└ vite.config.js
```

## src

Everything inside `src` folder is the main part of your application. Inside `src` folder, the most important parts are `app.html` and `routes` folders. 
- `app.html` is the root of your application (remember we mentioned that SvelteKit is for building web apps) 
- `lib` contains your library code (utilities and components) which you can import via the [`$lib`](https://kit.svelte.dev/docs/modules#$lib) alias, or packaged up for distribution using [`svelte-package`](https://kit.svelte.dev/docs/packaging)
	- `server` contains your server-only library code. It can be imported by using the [`$lib/server`](https://kit.svelte.dev/docs/server-only-modules) alias. SvelteKit will prevent you from importing these in client code
	- `client` contains your client-only library code. I like to place components that are meant to be reused across the app inside this folder. Svelte just recommends placing that inside `lib` folder but I like to create a separate folder for client related stuff. This folder could also potentially be removed and we can just use `components` instead since that is only client specific stuff. You'll see later on what I mean by this.
- `routes` folder contains the [routes](https://kit.svelte.dev/docs/routing) for you application. Meaning if you create `home` folder and a `+page.svelte` inside of if, then that route will be accessible via browser from the client. Similar routing is defined in Next.js projects
	- `api` folder contains the standalone endpoints if you ever need one. When will you need these endpoints? Sometimes it's useful to create these if you need to target specific endpoint from AWS lambda for example, or if you need it to submit data with your forms, or for example in some other [cases](https://www.youtube.com/watch?v=8OmsVZuuQMc&list=PLzJmSESVDpKrriNLIJfufXhvLhQmgeiuj&index=2)
- `params` contains any [param matchers](https://kit.svelte.dev/docs/advanced-routing#matching) your app needs
- `error.html` is the page that is rendered when everything else fails
- `hooks.client.js` contains your client [hooks](https://kit.svelte.dev/docs/hooks)
- `hooks.server.js` contains your server [hooks](https://kit.svelte.dev/docs/hooks)
- `service-worker.js` contains your [service worker](https://kit.svelte.dev/docs/service-workers)

## static

Any static assets that should be served as-is, like `robots.txt` or `favicon.png`, go in here.

## tests

Here you can place your test files. If you added [Playwright](https://playwright.dev/) for browser testing when you set up your project, the tests will live in this directory.

## package.json

Your `package.json` file must include `@sveltejs/kit`, `svelte` and `vite` as `devDependencies`.

When you create a project with `npm create svelte@latest`, you'll also notice that `package.json` includes `"type": "module"`. This means that `.js` files are interpreted as native JavaScript modules with `import` and `export` keywords. Legacy CommonJS files need a `.cjs` file extension.

## svelte.config.js

This file contains your Svelte and SvelteKit [configuration](https://kit.svelte.dev/docs/configuration).

## tsconfig.json 

This file (or `jsconfig.json`, if you prefer type-checked `.js` files over `.ts` files) configures TypeScript, if you added type checking during `npm create svelte@latest`. Since SvelteKit relies on certain configuration being set a specific way, it generates its own `.svelte-kit/tsconfig.json` file which your own config `extends`.

## vite.config.js

A SvelteKit project is really just a [Vite](https://vitejs.dev/) project that uses the [`@sveltejs/kit/vite`](https://kit.svelte.dev/docs/modules#sveltejs-kit-vite) plugin, along with any other [Vite configuration](https://vitejs.dev/config/).

# The blurry line between backend & frontend in SvelteKit
---
SvelteKit blurs the line between frontend and backend. It's not easily distinguishable whether something should run on the server or on the client. But the good news is, everything is in your hands and control. If you want something to be running only on the client you can easily do that. If you want to load the data and the contents of the web page on the backend to support SSR (which is better for SEO) you can also do that. Everything is in one codebase.
## Server side rendering

SvelteKit will render your page on the server first, load the data and send that HTML to the client where it's hydrated. You can disable this by adding the following code in `+page.ts` file:

```ts
export const ssr = false;
```

This will make your page an empty 'shell' and if you need any data from the backend you can call an API endpoint from within `+page.ts` file. This is useful if your page is unable to be rendered on the server (because you use browser-only globals like `document` for example), but in most situations [it's not recommended](https://kit.svelte.dev/docs/glossary#ssr).

If you add `export const ssr = false` to your root `+layout.js`, your entire app will only be rendered on the client — which essentially means you turn your app into an SPA.
## Client side rendering

Ordinarily, SvelteKit [hydrates](https://kit.svelte.dev/docs/glossary#hydration) your server-rendered HTML into an interactive client-side-rendered (CSR) page. Some pages don't require JavaScript at all — many blog posts and 'about' pages fall into this category. In these cases you can disable CSR:

```ts
export const csr = false;
```

Disabling CSR does not ship any JavaScript to the client. This means:
- The webpage should work with HTML and CSS only.
- `<script>` tags inside all Svelte components are removed.
- `<form>` elements cannot be [progressively enhanced](https://kit.svelte.dev/docs/form-actions#progressive-enhancement).
- Links are handled by the browser with a full-page navigation.

> [!tip]+ Running on server vs running on the client?
> Don't sweat about this. If you try to communicate with a database from within `+page.ts` file, Svelte compiler will warn you about it. If you're trying to do write interactive code on the server, SvelteKit will hydrate your server-rendered HTML and attach event listeners on the client side.
# Loading data
---
SvelteKit has `+page.ts`, `+page.server.ts` files and both files serve different purposes. Both files export `load` functions and here is the difference between them.
- `+page.js` and `+layout.js` files export *universal* `load` functions that run both on the server and in the browser.
- `+page.server.js` and `+layout.server.js` files export *server* `load` functions that only run server-side

### When does which load function run?

Server `load` functions _always_ run on the server.

By default, universal `load` functions run on the server during SSR when the user first visits your page. They will then run again during hydration, reusing any responses from fetch requests. All subsequent invocations of universal `load` functions happen in the browser. You can customize the behavior through [page options](https://kit.svelte.dev/docs/page-options). If you disable SSR, you'll get an SPA and universal `load` functions _always_ run on the client. If a route contains both universal and server `load` functions, the server `load` runs first.

A `load` function is invoked at runtime, unless you [prerender](https://kit.svelte.dev/docs/page-options#prerender) the page — in that case, it's invoked at build time.
## Input

Both universal and server `load` functions have access to properties describing the request (`params`, `route` and `url`) and various functions (`fetch`, `setHeaders`, `parent`, `depends` and `untrack`). These are described in the following sections.

Server `load` functions are called with a `ServerLoadEvent`, which inherits `clientAddress`, `cookies`, `locals`, `platform` and `request` from `RequestEvent`.

Universal `load` functions are called with a `LoadEvent`, which has a `data` property. If you have `load` functions in both `+page.js` and `+page.server.js` (or `+layout.js` and `+layout.server.js`), the return value of the server `load` function is the `data` property of the universal `load` function's argument.
## Output

A universal `load` function can return an object containing any values, including things like custom classes and component constructors.

A server `load` function must return data that can be serialized with [devalue](https://github.com/rich-harris/devalue) — anything that can be represented as JSON plus things like `BigInt`, `Date`, `Map`, `Set` and `RegExp`, or repeated/cyclical references — so that it can be transported over the network. Your data can include [promises](https://kit.svelte.dev/docs/load#streaming-with-promises), in which case it will be streamed to browsers.
## When to use which?

Server `load` functions are convenient when you need to access data directly from a database or filesystem, or need to use private environment variables.

Universal `load` functions are useful when you need to `fetch` data from an external API and don't need private credentials, since SvelteKit can get the data directly from the API rather than going via your server. They are also useful when you need to return something that can't be serialized, such as a Svelte component constructor.

In rare cases, you might need to use both together — for example, you might need to return an instance of a custom class that was initialised with data from your server. When using both, the server `load` return value is _not_ passed directly to the page, but to the universal `load` function (as the `data` property):

`src/routes/+page.server.js`
```ts
/** @type {import('./$types').PageServerLoad} */
export async function load() {
	return {
		serverMessage: 'hello from server load function'
	};
}
```

`src/routes/+page.js`
```ts
/** @type {import('./$types').PageServerLoad} */
export async function load({ data }) {
	return {
		serverMessage: data.serverMessage,
		universalMessage: 'hello from universal load function'
	};
}
```

# Architecture
---
## Hexagonal architecture

When building an application it's essential to organize your code in a way that promotes maintainability, scalability, and readability. I personally like the [Hexagonal (Ports & Adapter) architecture](https://medium.com/idealo-tech-blog/hexagonal-ports-adapters-architecture-e3617bcf00a0) because it offers all these traits. Read more about it [[Hexagonal (Ports & Adapters)|here]].

To sum it up, in this architecture we have "ports", which refer to interfaces that define a contract of how external components communicate with the core and we have "adapters" which refer to classes that implement these interfaces (ports) and provide necessary logic to interact with external components.

Inside these external components, we use an interface (port) and with the help of dependency injection we can use decide which adapter to use. For example we can have two adapters for Payments; one can use MongoDB under the hood and the other one can use PostgreSQL, and with just simple one-liner code change we can switch if we're using the PostgreSQL Adapter or MongoDB adapter.

Let's look at concrete examples in SvelteKit project. Our `routes` with individual pages and `api` folder represent the *presentation layer* on the "backend". There are load functions and API endpoints that serve as entry point to the data behind on backend. Presentation layer should use a port to communicate to the *domain* layer. We can inject the adapter with dependency injection. 

### Presentation layer

By utilizing ports right here, we can easily transition to a console or some different framework in the future if we want to. Think about it. Define interfaces (ports) to communicate with the service layer and in the future you can build a completely different UI and just place you domain (service) layer inside the Express app if you want to. Just use the same interfaces to communicate with your domain (service) layer. You can even build the two apps in the same time.

### Domain layer

The whole point of this architecture is to have clean domain layer that knows **nothing** about the outside world. The whole domain is written in JavaScript and tomorrow, if you need, you can use completely different framework for it. Highly flexible approach. Also this allows testing the domain layer without the need to test the presentation and repository (data) layers. Domain only knows about domain. In terms of SvelteKit application, everything should live in `services` or `domain` folder. Name it however you want.

### Repository layer

Data or repository layer is in the output layer. The domain layer should communicate with it only with the ports defined. Then in the future, you can build two different implementations of specific repository and switch which one you're using if you ever need to. You can place this inside the `repository` or `data` folder and you can separate further by a data source you're using.

### Conclusion

Using this architecture / pattern you will get scalable and maintainable application. 

---
## Architecture for smaller / short lived apps

Hexagonal architecture might be an overkill for small apps that are just starting out, specifically because of all the boilerplate code and setup. If you're short on time and need to build something fast you can define services (for business logic) and go with simple architecture and folder structure.

Where to define models for this approach? You could place them inside `lib/domain` folder and now the question is should you reuse the same models on the client? You could separate the two and create specific models for the client inside `lib/client` folder but then you would probably have the similar, if not the same models both on the server.

The point of this approach is to speed up your development. Don't go overboard in terms of thinking too much about scaling and preparing the app for something it may someday become. Instead, use best practices and when the time for that comes, if it comes, then think about transitioning to Hexagonal or another pattern.

> [!info] 
> For smaller apps you can use the same models on *frontend* and *backend*. It's the same codebase, might as well use it to your advantage.

I would prefer everything to run on the server if possible, so therefore I would place the models and types inside `lib` folder somewhere. Perhaps `lib/models` or `lib/types`. Domain and data models can be separated in here.

### Business logic

Where should we place a complex business logic? For example we need to change some data in the database and to do that we need to target a specific endpoint inside our `api` folder. Complex business logic should ideally be separated from presentation logic and placed in dedicated modules or services. For that we could use `services` or `utils` folders to contain modules or services responsible for handling complex business logic. They can communicate with each other to ensure better separation of concerns and scalability.

### Data layer

Keep your data access and manipulation logic separate from you business logic. You can use dependency injection or inversion of control principles to manage dependencies between modules or services. This can help decouple your business logic from specific implementations and make it easier to unit test and maintain and switch between database storage options easily.

# ORMs
---
For ORMs on the database layer I used [[Prisma]] and [[TypeORM]]. Both are good solutions and both have their ups and downs.

# Testing
---
How your application is structured and where logic is defined will determine the best way to ensure it is properly tested. It is important to note that not all logic belongs within a component - this includes concerns such as data transformation, cross-component state management, and logging, among others.

A Svelte application will typically have three different types of tests: 
- Unit
- Component
- and End-to-End (E2E)

_Unit Tests_: Focus on testing business logic in isolation. Often this is validating individual functions and edge cases. By minimizing the surface area of these tests they can be kept lean and fast, and by extracting as much logic as possible from your Svelte components more of your application can be covered using them. When creating a new SvelteKit project, you will be asked whether you would like to setup [Vitest](https://vitest.dev/) for unit testing. There are a number of other test runners that could be used as well.

_Component Tests_: Validating that a Svelte component mounts and interacts as expected throughout its lifecycle requires a tool that provides a Document Object Model (DOM). Components can be compiled (since Svelte is a compiler and not a normal library) and mounted to allow asserting against element structure, listeners, state, and all the other capabilities provided by a Svelte component. Tools for component testing range from an in-memory implementation like jsdom paired with a test runner like [Vitest](https://vitest.dev/) to solutions that leverage an actual browser to provide a visual testing capability such as [Playwright](https://playwright.dev/docs/test-components) or [Cypress](https://www.cypress.io/).

_End-to-End Tests_: To ensure your users are able to interact with your application it is necessary to test it as a whole in a manner as close to production as possible. This is done by writing end-to-end (E2E) tests which load and interact with a deployed version of your application in order to simulate how the user will interact with your application. When creating a new SvelteKit project, you will be asked whether you would like to setup [Playwright](https://playwright.dev/) for end-to-end testing. There are many other E2E test libraries available for use as well.

# GraphQL
---
Coming soon...