# Error Handling Guide

Error handling is a crucial aspect of building robust React Router applications. This guide will walk you through various strategies for handling errors, displaying error messages, and implementing graceful error recovery.

## Table of Contents

1. [Error Boundaries](#error-boundaries)
2. [Displaying Error Messages](#displaying-error-messages)
3. [Handling Errors in Loaders and Actions](#handling-errors-in-loaders-and-actions)
4. [Custom Error Pages](#custom-error-pages)
5. [Graceful Error Recovery](#graceful-error-recovery)

## Error Boundaries

Error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed. React Router integrates well with error boundaries to provide a seamless error handling experience.

To implement an error boundary in your React Router application:

1. Create an error boundary component:

```jsx
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by ErrorBoundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

2. Wrap your routes or components with the error boundary:

```jsx
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import ErrorBoundary from './ErrorBoundary';

function App() {
  return (
    <Router>
      <ErrorBoundary>
        <Routes>
          {/* Your routes here */}
        </Routes>
      </ErrorBoundary>
    </Router>
  );
}
```

## Displaying Error Messages

React Router provides built-in support for displaying error messages through the `useRouteError` hook. This hook allows you to access error information thrown by loaders, actions, or rendering.

Here's how you can use it:

```jsx
import { useRouteError } from 'react-router-dom';

function ErrorPage() {
  const error = useRouteError();

  return (
    <div>
      <h1>Oops! An error occurred.</h1>
      <p>{error.message || 'Unknown error'}</p>
    </div>
  );
}
```

You can then use this `ErrorPage` component in your route configuration:

```jsx
import { createBrowserRouter } from 'react-router-dom';
import ErrorPage from './ErrorPage';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      // Other routes
    ],
  },
]);
```

## Handling Errors in Loaders and Actions

Loaders and actions in React Router can throw errors, which will be caught and rendered by the nearest error boundary. You can throw responses or errors directly from your loaders and actions:

```jsx
import { json } from 'react-router-dom';

async function loader({ params }) {
  const user = await getUser(params.id);
  if (!user) {
    throw json(
      { message: 'User not found' },
      { status: 404 }
    );
  }
  return { user };
}

async function action({ request }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  try {
    await updateUser(updates);
    return json({ success: true });
  } catch (error) {
    throw json(
      { message: 'Failed to update user' },
      { status: 400 }
    );
  }
}
```

## Custom Error Pages

You can create custom error pages for specific error types or status codes. Here's an example of how to implement a custom 404 Not Found page:

```jsx
import { useRouteError, isRouteErrorResponse } from 'react-router-dom';

function ErrorPage() {
  const error = useRouteError();

  if (isRouteErrorResponse(error) && error.status === 404) {
    return (
      <div>
        <h1>404 - Not Found</h1>
        <p>Sorry, the page you are looking for does not exist.</p>
      </div>
    );
  }

  return (
    <div>
      <h1>Oops! An error occurred.</h1>
      <p>{error.message || 'Unknown error'}</p>
    </div>
  );
}
```

## Graceful Error Recovery

To implement graceful error recovery in your React Router application:

1. Use try-catch blocks in your components to handle specific errors.
2. Implement retry mechanisms for failed API calls or data fetching.
3. Provide clear instructions to users on how to recover from errors.
4. Use the `useNavigate` hook to redirect users to safe pages when unrecoverable errors occur.

Example of error recovery with retry mechanism:

```jsx
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';

function DataFetchingComponent() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [retries, setRetries] = useState(0);
  const navigate = useNavigate();

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('/api/data');
        if (!response.ok) {
          throw new Error('Failed to fetch data');
        }
        const result = await response.json();
        setData(result);
        setError(null);
      } catch (err) {
        setError(err.message);
        if (retries < 3) {
          setTimeout(() => {
            setRetries(retries + 1);
          }, 1000 * (retries + 1));
        } else {
          navigate('/error', { state: { message: err.message } });
        }
      }
    };

    fetchData();
  }, [retries, navigate]);

  if (error) {
    return (
      <div>
        <p>Error: {error}</p>
        {retries < 3 && <p>Retrying... Attempt {retries + 1}/3</p>}
      </div>
    );
  }

  if (!data) {
    return <p>Loading...</p>;
  }

  return <div>{/* Render your data here */}</div>;
}
```

By implementing these error handling strategies, you can create a more robust and user-friendly React Router application that gracefully handles errors and provides a better experience for your users.