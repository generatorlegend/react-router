# Navigation Guide

React Router provides several ways to handle navigation within your application. This guide covers the main navigation methods, including Link components, the useNavigate hook, and programmatic navigation.

## Table of Contents

1. [Link Component](#link-component)
2. [NavLink Component](#navlink-component)
3. [useNavigate Hook](#usenavigate-hook)
4. [Programmatic Navigation](#programmatic-navigation)
5. [Navigation with State](#navigation-with-state)
6. [Relative Links](#relative-links)
7. [Best Practices](#best-practices)

## Link Component

The `Link` component is the primary way to navigate between routes in React Router. It renders an accessible `<a>` element with a real href that points to the resource it's linking to.

```jsx
import { Link } from "react-router-dom";

function Navigation() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  );
}
```

## NavLink Component

`NavLink` is a special version of `Link` that adds styling attributes when the route it links to is active. This is useful for navigation menus.

```jsx
import { NavLink } from "react-router-dom";

function Navigation() {
  return (
    <nav>
      <NavLink to="/" end>
        {({ isActive }) => (
          <span className={isActive ? "active" : ""}>Home</span>
        )}
      </NavLink>
      <NavLink to="/about" className={({ isActive }) => isActive ? "active" : ""}>
        About
      </NavLink>
    </nav>
  );
}
```

## useNavigate Hook

The `useNavigate` hook provides a programmatic way to navigate. It returns a function that allows you to navigate to a new location.

```jsx
import { useNavigate } from "react-router-dom";

function LoginButton() {
  let navigate = useNavigate();

  function handleClick() {
    navigate("/dashboard");
  }

  return <button onClick={handleClick}>Login</button>;
}
```

## Programmatic Navigation

You can use the `useNavigate` hook for more complex navigation scenarios, such as navigating after form submission or based on certain conditions.

```jsx
import { useNavigate } from "react-router-dom";

function SearchForm() {
  let navigate = useNavigate();

  function handleSubmit(event) {
    event.preventDefault();
    let searchQuery = event.target.elements.search.value;
    navigate(`/search?q=${searchQuery}`);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" name="search" />
      <button type="submit">Search</button>
    </form>
  );
}
```

## Navigation with State

You can pass state to the new location using the `state` option with `useNavigate` or the `state` prop on `Link`.

```jsx
import { useNavigate, Link } from "react-router-dom";

function StateExample() {
  let navigate = useNavigate();

  function handleClick() {
    navigate("/user", { state: { userId: 123 } });
  }

  return (
    <div>
      <button onClick={handleClick}>Go to User (useNavigate)</button>
      <Link to="/user" state={{ userId: 456 }}>Go to User (Link)</Link>
    </div>
  );
}
```

## Relative Links

React Router supports relative links, which are resolved relative to the current route.

```jsx
import { Link } from "react-router-dom";

function RelativeExample() {
  return (
    <div>
      <Link to="..">Go up one level</Link>
      <Link to="../sibling">Go to sibling route</Link>
      <Link to="child">Go to child route</Link>
    </div>
  );
}
```

## Best Practices

1. Use `Link` for navigation whenever possible, as it provides the best accessibility and SEO benefits.
2. Use `NavLink` for navigation menus to easily style active links.
3. Use `useNavigate` for programmatic navigation, especially after form submissions or in response to user actions.
4. Always consider the user experience when implementing navigation. Provide clear indicators of the current location and make navigation intuitive.
5. Use relative links when appropriate to make your routes more flexible and easier to maintain.
6. When using `useNavigate`, be mindful of the warning about calling navigate in render. Always use it within event handlers or effects.

Remember, good navigation is key to creating a smooth and intuitive user experience in your React Router applications.