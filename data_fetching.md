# Data Fetching in React Router

React Router provides powerful data fetching capabilities that allow you to efficiently manage and load data in your application. This guide will cover the main concepts and techniques for data fetching in React Router, including loaders, actions, and fetchers.

## Table of Contents

1. [Loaders](#loaders)
2. [Actions](#actions)
3. [Fetchers](#fetchers)
4. [Deferred Data Loading](#deferred-data-loading)

## Loaders

Loaders are functions that load data for a route before it renders. They are defined in your route configuration and can return data that will be available to your components via the `useLoaderData` hook.

### Defining a Loader

```javascript
let router = createBrowserRouter([
  {
    path: "/todos",
    loader: todosLoader,
    Component: TodosList,
  },
]);

async function todosLoader() {
  await sleep();
  return getTodos();
}
```

### Using Loader Data in Components

```javascript
function TodosList() {
  let todos = useLoaderData();
  
  return (
    <ul>
      {Object.entries(todos).map(([id, todo]) => (
        <li key={id}>
          <TodoItem id={id} todo={todo} />
        </li>
      ))}
    </ul>
  );
}
```

## Actions

Actions handle data mutations in your application. They are typically triggered by form submissions and can update your application state.

### Defining an Action

```javascript
let router = createBrowserRouter([
  {
    path: "/todos",
    action: todosAction,
    Component: TodosList,
  },
]);

async function todosAction({ request }) {
  await sleep();
  let formData = await request.formData();
  
  if (formData.get("action") === "delete") {
    let id = formData.get("todoId");
    if (typeof id === "string") {
      deleteTodo(id);
      return { ok: true };
    }
  }
  
  let todo = formData.get("todo");
  if (typeof todo === "string") {
    addTodo(todo);
  }
  
  return new Response(null, {
    status: 302,
    headers: { Location: "/todos" },
  });
}
```

### Using Actions in Components

```javascript
function TodosList() {
  return (
    <Form method="post">
      <input type="hidden" name="action" value="add" />
      <input name="todo" />
      <button type="submit">Add</button>
    </Form>
  );
}
```

## Fetchers

Fetchers allow you to perform data operations without navigating away from the current page. They're useful for optimistic UI updates and background data synchronization.

### Using Fetchers

```javascript
function TodoItem({ id, todo }) {
  let fetcher = useFetcher();
  
  let isDeleting = fetcher.formData != null;
  return (
    <>
      <Link to={`/todos/${id}`}>{todo}</Link>
      <fetcher.Form method="post" style={{ display: "inline" }}>
        <input type="hidden" name="action" value="delete" />
        <button type="submit" name="todoId" value={id} disabled={isDeleting}>
          {isDeleting ? "Deleting..." : "Delete"}
        </button>
      </fetcher.Form>
    </>
  );
}
```

## Deferred Data Loading

React Router supports deferred data loading, allowing you to load critical data immediately while deferring less important data. This can significantly improve the perceived performance of your application.

### Defining Deferred Data

```javascript
export async function deferredLoader() {
  return defer({
    critical1: await resolve("Critical 1", 250),
    critical2: await resolve("Critical 2", 500),
    lazy1: resolve("Lazy 1", 1000),
    lazy2: resolve("Lazy 2", 1500),
  });
}
```

### Rendering Deferred Data

```javascript
export function DeferredPage() {
  let data = useLoaderData();
  return (
    <div>
      {/* Critical data renders immediately */}
      <p>{data.critical1}</p>
      <p>{data.critical2}</p>

      {/* Deferred data renders when ready */}
      <React.Suspense fallback={<p>loading 1...</p>}>
        <Await resolve={data.lazy1}>
          <RenderAwaitedData />
        </Await>
      </React.Suspense>

      <React.Suspense fallback={<p>loading 2...</p>}>
        <Await resolve={data.lazy2}>
          <RenderAwaitedData />
        </Await>
      </React.Suspense>
    </div>
  );
}

function RenderAwaitedData() {
  let data = useAsyncValue();
  return <p>{data}</p>;
}
```

By leveraging these data fetching techniques in React Router, you can create more responsive and efficient applications that provide a smooth user experience while managing complex data flows.