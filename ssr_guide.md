---
title: Server-Side Rendering (SSR) Guide
---

# Server-Side Rendering (SSR) Guide

This guide will walk you through implementing server-side rendering (SSR) with React Router. We'll cover setup, handling data loading on the server, and hydrating the application on the client.

## Table of Contents

1. [Setup](#setup)
2. [Server-Side Rendering](#server-side-rendering)
3. [Data Loading on the Server](#data-loading-on-the-server)
4. [Hydration on the Client](#hydration-on-the-client)
5. [Advanced Topics](#advanced-topics)

## Setup

To get started with SSR using React Router, you'll need to set up your project with the necessary dependencies:

```bash
npm install react-router-dom
```

## Server-Side Rendering

React Router provides tools to help you implement SSR. The main component you'll use is `StaticRouter` on the server side.

Here's a basic example of how to set up SSR with React Router:

```jsx
import { StaticRouter } from 'react-router-dom/server';
import { renderToString } from 'react-dom/server';
import App from './App';

function handleRequest(req, res) {
  let html = renderToString(
    <StaticRouter location={req.url}>
      <App />
    </StaticRouter>
  );
  
  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>My SSR App</title>
      </head>
      <body>
        <div id="root">${html}</div>
      </body>
    </html>
  `);
}
```

## Data Loading on the Server

For data loading on the server, React Router provides the `createStaticHandler` function. This allows you to define and execute data loading logic for your routes on the server.

Here's an example of how to use `createStaticHandler`:

```jsx
import { createStaticHandler } from 'react-router-dom/server';
import { routes } from './routes';

async function handleRequest(req, res) {
  let handler = createStaticHandler(routes);
  let context = await handler.query(req);
  
  if (context instanceof Response) {
    throw context;
  }
  
  let html = renderToString(
    <StaticRouterProvider
      router={handler.createStaticRouter(context)}
      context={context}
    />
  );
  
  res.send(`<!DOCTYPE html><html><body><div id="root">${html}</div></body></html>`);
}
```

## Hydration on the Client

To hydrate your application on the client, use the `HydratedRouter` component:

```jsx
import { HydratedRouter } from 'react-router-dom';
import { hydrateRoot } from 'react-dom/client';
import App from './App';

hydrateRoot(
  document.getElementById('root'),
  <HydratedRouter>
    <App />
  </HydratedRouter>
);
```

## Advanced Topics

### Error Handling

React Router provides mechanisms for handling errors during SSR. You can use the `errorHandler` function to customize error handling:

```jsx
let errorHandler = (error, { request }) => {
  if (serverMode !== ServerMode.Test && !request.signal.aborted) {
    console.error(
      isRouteErrorResponse(error) && error.error ? error.error : error
    );
  }
};
```

### Custom Data Loading

You can customize data loading behavior using the `handleDataRequest` function:

```jsx
if (build.entry.module.handleDataRequest) {
  response = await build.entry.module.handleDataRequest(response, {
    context: loadContext,
    params: singleFetchMatches ? singleFetchMatches[0].params : {},
    request,
  });
}
```

### Critical CSS

For optimized loading, you can include critical CSS in your SSR setup:

```jsx
if (build.unstable_getCriticalCss) {
  criticalCss = await build.unstable_getCriticalCss({ pathname });
}
```

Remember to thoroughly test your SSR implementation to ensure it works correctly across different routes and scenarios.