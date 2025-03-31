# Using React Router with TypeScript

React Router can be used seamlessly with TypeScript to provide type-safe routing in your React applications. This guide will walk you through the process of setting up and using React Router with TypeScript, including type definitions for key components and hooks, and examples of type-safe routing configurations.

## Installation

First, make sure you have React Router and its TypeScript definitions installed:

```bash
npm install react-router-dom @types/react-router-dom
```

## Basic Setup

To start using React Router with TypeScript, import the necessary components and types from `react-router-dom`:

```typescript
import { BrowserRouter, Route, Routes, Link } from 'react-router-dom';
```

## Type Definitions for Key Components

React Router provides TypeScript definitions for its components out of the box. Here are some of the most commonly used types:

```typescript
import type {
  RouteProps,
  RouteComponentProps,
  LinkProps,
  NavLinkProps,
  NavigateOptions,
} from 'react-router-dom';
```

## Creating Type-Safe Routes

When defining your routes, you can use TypeScript to ensure type safety:

```typescript
import React from 'react';
import { Route, Routes } from 'react-router-dom';

const Home: React.FC = () => <h1>Home</h1>;
const About: React.FC = () => <h1>About</h1>;
const NotFound: React.FC = () => <h1>404 Not Found</h1>;

const App: React.FC = () => (
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="*" element={<NotFound />} />
  </Routes>
);
```

## Using Hooks with TypeScript

React Router's hooks can be used with TypeScript for added type safety. Here are some examples:

### useParams

```typescript
import { useParams } from 'react-router-dom';

interface UserParams {
  id: string;
}

const UserProfile: React.FC = () => {
  const { id } = useParams<UserParams>();
  return <h1>User Profile for ID: {id}</h1>;
};
```

### useNavigate

```typescript
import { useNavigate } from 'react-router-dom';

const NavigationComponent: React.FC = () => {
  const navigate = useNavigate();

  const handleClick = () => {
    navigate('/about');
  };

  return <button onClick={handleClick}>Go to About</button>;
};
```

### useLocation

```typescript
import { useLocation } from 'react-router-dom';

const LocationComponent: React.FC = () => {
  const location = useLocation();
  return <p>Current path: {location.pathname}</p>;
};
```

## Type-Safe Link Components

When using the `Link` component, you can ensure type safety for the `to` prop:

```typescript
import { Link } from 'react-router-dom';

const TypeSafeLink: React.FC = () => (
  <Link to="/about">About</Link>
);
```

## Advanced Usage: Route Config Objects

For more complex routing configurations, you can create type-safe route config objects:

```typescript
import React from 'react';
import { RouteObject } from 'react-router-dom';

const Home: React.FC = () => <h1>Home</h1>;
const About: React.FC = () => <h1>About</h1>;
const NotFound: React.FC = () => <h1>404 Not Found</h1>;

const routes: RouteObject[] = [
  {
    path: '/',
    element: <Home />,
  },
  {
    path: '/about',
    element: <About />,
  },
  {
    path: '*',
    element: <NotFound />,
  },
];

// Use these routes with createBrowserRouter or useRoutes
```

## Conclusion

By leveraging TypeScript with React Router, you can create more robust and maintainable routing configurations in your React applications. TypeScript provides additional safety and improved developer experience when working with routes, components, and hooks provided by React Router.

Remember to refer to the official React Router documentation for the most up-to-date information on available types and best practices for using TypeScript with React Router.