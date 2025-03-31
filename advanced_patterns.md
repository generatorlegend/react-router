# Advanced Patterns in React Router

React Router is a powerful library that enables complex routing scenarios in React applications. This guide covers advanced patterns and techniques to help you build more sophisticated routing solutions.

## Modal Routes

Modal routes allow you to display content in a modal dialog while maintaining the current route underneath. This pattern is useful for showing additional information or forms without navigating away from the current page.

### Implementation

1. Create a modal component that renders its children:

```jsx
function Modal({ children }) {
  return (
    <div className="modal">
      <div className="modal-content">
        {children}
      </div>
    </div>
  );
}
```

2. Set up your routes to include a modal route:

```jsx
import { Routes, Route, useLocation } from 'react-router-dom';

function App() {
  const location = useLocation();
  const background = location.state && location.state.background;

  return (
    <>
      <Routes location={background || location}>
        <Route path="/" element={<Home />} />
        <Route path="/products" element={<ProductList />} />
        <Route path="/products/:id" element={<ProductDetails />} />
      </Routes>

      {background && (
        <Routes>
          <Route path="/products/:id" element={
            <Modal>
              <ProductDetails />
            </Modal>
          } />
        </Routes>
      )}
    </>
  );
}
```

3. When navigating to a modal route, pass the current location as state:

```jsx
import { Link, useLocation } from 'react-router-dom';

function ProductList() {
  const location = useLocation();

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          <Link
            to={`/products/${product.id}`}
            state={{ background: location }}
          >
            {product.name}
          </Link>
        </li>
      ))}
    </ul>
  );
}
```

## Nested Layouts

Nested layouts allow you to create complex page structures with shared components across multiple routes. This pattern is useful for creating consistent UI elements across different sections of your application.

### Implementation

1. Create layout components for different levels of nesting:

```jsx
function MainLayout({ children }) {
  return (
    <div className="main-layout">
      <header>
        <nav>{/* Add navigation menu items */}</nav>
      </header>
      <main>{children}</main>
      <footer>{/* Add footer content */}</footer>
    </div>
  );
}

function ProductLayout({ children }) {
  return (
    <div className="product-layout">
      <aside>{/* Add product category sidebar */}</aside>
      <section>{children}</section>
    </div>
  );
}
```

2. Set up nested routes using the `Outlet` component:

```jsx
import { Routes, Route, Outlet } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route element={<MainLayout />}>
        <Route path="/" element={<Home />} />
        <Route path="about" element={<About />} />
        <Route path="products" element={<ProductLayout />}>
          <Route index element={<ProductList />} />
          <Route path=":id" element={<ProductDetails />} />
        </Route>
      </Route>
    </Routes>
  );
}

function MainLayout() {
  return (
    <div className="main-layout">
      <header>{/* Add header content */}</header>
      <Outlet />
      <footer>{/* Add footer content */}</footer>
    </div>
  );
}

function ProductLayout() {
  return (
    <div className="product-layout">
      <aside>{/* Add product category sidebar */}</aside>
      <Outlet />
    </div>
  );
}
```

## Authentication Flows

Implementing authentication flows in React Router involves protecting routes, redirecting unauthenticated users, and managing user sessions.

### Implementation

1. Create a context for managing authentication state:

```jsx
import React, { createContext, useState, useContext } from 'react';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => {
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

2. Create a `ProtectedRoute` component to guard authenticated routes:

```jsx
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from './AuthContext';

function ProtectedRoute({ children }) {
  const { user } = useAuth();
  const location = useLocation();

  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}
```

3. Set up routes with authentication:

```jsx
import { Routes, Route } from 'react-router-dom';
import { AuthProvider } from './AuthContext';

function App() {
  return (
    <AuthProvider>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/dashboard" element={
          <ProtectedRoute>
            <Dashboard />
          </ProtectedRoute>
        } />
        <Route path="/profile" element={
          <ProtectedRoute>
            <Profile />
          </ProtectedRoute>
        } />
      </Routes>
    </AuthProvider>
  );
}
```

4. Implement login and logout functionality:

```jsx
import { useNavigate, useLocation } from 'react-router-dom';
import { useAuth } from './AuthContext';

function Login() {
  const navigate = useNavigate();
  const location = useLocation();
  const { login } = useAuth();

  const handleLogin = (credentials) => {
    // Perform authentication API call
    const userData = { /* user data from API */ };
    login(userData);

    const from = location.state?.from?.pathname || '/dashboard';
    navigate(from, { replace: true });
  };

  return (
    // Login form implementation
  );
}

function Dashboard() {
  const { user, logout } = useAuth();
  const navigate = useNavigate();

  const handleLogout = () => {
    logout();
    navigate('/');
  };

  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <button onClick={handleLogout}>Logout</button>
      {/* Dashboard content */}
    </div>
  );
}
```

By implementing these advanced patterns, you can create more sophisticated and user-friendly applications using React Router. Remember to adapt these patterns to your specific use cases and requirements.