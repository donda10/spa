# Simple React SPA

## Aim
To create a very basic React Single Page Application (SPA) with protected dashboard routes using React Router.

## Procedure
1. Create a new React project using Vite or Create React App.
2. Install React Router:
   ```sh
   npm install react-router-dom
## Program

```


import React, { useState, useEffect, createContext, useContext } from 'react';
import { BrowserRouter, Routes, Route, Link, Navigate, Outlet, useNavigate } from 'react-router-dom';

// Simple Auth context using localStorage
const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(() => {
    const saved = localStorage.getItem('user');
    return saved ? JSON.parse(saved) : null;
  });

  useEffect(() => {
    if (user) localStorage.setItem('user', JSON.stringify(user));
    else localStorage.removeItem('user');
  }, [user]);

  const login = (name) => setUser({ name });
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  return useContext(AuthContext);
}

// Protected Route component
function Protected({ children }) {
  const auth = useAuth();
  if (!auth.user) {
    return <Navigate to="/login" replace />;
  }
  return children;
}

// Simple navbar
function Nav() {
  const auth = useAuth();
  return (
    <div style={{ padding: 10, borderBottom: '1px solid #ddd' }}>
      <Link to="/" style={{ marginRight: 10 }}>Home</Link>
      <Link to="/dashboard" style={{ marginRight: 10 }}>Dashboard</Link>
      {auth.user ? (
        <span style={{ marginLeft: 20 }}>Hello, {auth.user.name}</span>
      ) : (
        <Link to="/login" style={{ marginLeft: 20 }}>Login</Link>
      )}
    </div>
  );
}

function Home() {
  return (
    <div style={{ padding: 20 }}>
      <h1>Home</h1>
      <p>This is a simple beginner React SPA with protected nested routes.</p>
    </div>
  );
}

function Login() {
  const [name, setName] = useState('');
  const auth = useAuth();
  const navigate = useNavigate();

  function handleSubmit(e) {
    e.preventDefault();
    if (!name) return alert('Please enter a name');
    auth.login(name);
    navigate('/dashboard');
  }

  return (
    <div style={{ padding: 20 }}>
      <h1>Login</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label>Name: </label>
          <input value={name} onChange={(e) => setName(e.target.value)} />
        </div>
        <button type="submit" style={{ marginTop: 10 }}>Login</button>
      </form>
    </div>
  );
}

// Dashboard layout with nested routes
function DashboardLayout() {
  const auth = useAuth();
  const navigate = useNavigate();

  function doLogout() {
    auth.logout();
    navigate('/');
  }

  return (
    <div style={{ padding: 20 }}>
      <h1>Dashboard</h1>
      <div style={{ marginBottom: 10 }}>
        <Link to="profile" style={{ marginRight: 10 }}>Profile</Link>
        <Link to="settings" style={{ marginRight: 10 }}>Settings</Link>
        <Link to="notifications">Notifications</Link>
      </div>
      <div style={{ marginBottom: 10 }}>
        <button onClick={doLogout}>Logout</button>
      </div>

      {/* Nested pages render here */}
      <div style={{ border: '1px solid #eee', padding: 10 }}>
        <Outlet />
      </div>
    </div>
  );
}

function Profile() {
  const auth = useAuth();
  return (
    <div>
      <h2>Profile</h2>
      <p>Name: {auth.user?.name}</p>
    </div>
  );
}

function Settings() {
  return (
    <div>
      <h2>Settings</h2>
      <p>Simple settings page (no real settings).</p>
    </div>
  );
}

function Notifications() {
  return (
    <div>
      <h2>Notifications</h2>
      <ul>
        <li>You have 1 new message.</li>
        <li>Your account is active.</li>
      </ul>
    </div>
  );
}

function NotFound() {
  return (
    <div style={{ padding: 20 }}>
      <h1>404 Not Found</h1>
      <p>Sorry, page not found.</p>
    </div>
  );
}

export default function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Nav />
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/login" element={<Login />} />

          <Route path="/dashboard" element={<Protected><DashboardLayout /></Protected>}>
            <Route index element={<Profile />} />
            <Route path="profile" element={<Profile />} />
            <Route path="settings" element={<Settings />} />
            <Route path="notifications" element={<Notifications />} />
          </Route>

          <Route path="*" element={<NotFound />} />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}




```

##Output

<img width="1847" height="1064" alt="image" src="https://github.com/user-attachments/assets/f1648e9d-4c31-4a0b-8c88-f9bb2672ac50" />
