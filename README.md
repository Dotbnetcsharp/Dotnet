// File: package.json (partial)
// Make sure you have these installed:
{
  "dependencies": {
    "@azure/msal-browser": "^3.0.0",
    "@azure/msal-react": "^2.0.0",
    "react-router-dom": "^6.0.0"
  },
  "devDependencies": {
    "@types/react-router-dom": "^5.3.3"
  }
}

// =========================================
// File: src/authConfig.ts
export const msalConfig = {
  auth: {
    clientId: "YOUR_CLIENT_ID", // replace with actual
    authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
    redirectUri: "http://localhost:3000/auth"
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: false
  }
};

export const loginRequest = {
  scopes: ["User.Read"]
};

// =========================================
// File: src/index.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import { MsalProvider } from "@azure/msal-react";
import { PublicClientApplication } from "@azure/msal-browser";
import App from "./App";
import { msalConfig } from "./authConfig";

const msalInstance = new PublicClientApplication(msalConfig);

const root = ReactDOM.createRoot(document.getElementById("root") as HTMLElement);
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <MsalProvider instance={msalInstance}>
        <App />
      </MsalProvider>
    </BrowserRouter>
  </React.StrictMode>
);

// =========================================
// File: src/App.tsx
import React from "react";
import { Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import AuthCallback from "./pages/AuthCallback";

const App: React.FC = () => {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/auth" element={<AuthCallback />} />
    </Routes>
  );
};

export default App;

// =========================================
// File: src/pages/Home.tsx
import React from "react";
import LoginButton from "../components/LoginButton";
import Profile from "../components/Profile";

const Home: React.FC = () => {
  return (
    <div style={{ padding: "2rem" }}>
      <h1>Microsoft SSO Login</h1>
      <LoginButton />
      <Profile />
    </div>
  );
};

export default Home;

// =========================================
// File: src/pages/AuthCallback.tsx
import React, { useEffect } from "react";
import { useMsal } from "@azure/msal-react";

const AuthCallback: React.FC = () => {
  const { instance } = useMsal();

  useEffect(() => {
    instance.handleRedirectPromise()
      .then((res) => {
        if (res) {
          console.log("Logged in as:", res.account);
        }
        window.location.replace("/");
      })
      .catch((err) => {
        console.error("Redirect error:", err);
      });
  }, [instance]);

  return <div>Signing in with Microsoft...</div>;
};

export default AuthCallback;

// =========================================
// File: src/components/LoginButton.tsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";

const LoginButton: React.FC = () => {
  const { instance } = useMsal();

  const handleLogin = () => {
    instance.loginRedirect(loginRequest);
  };

  return <button onClick={handleLogin}>Sign in with Microsoft</button>;
};

export default LoginButton;

// =========================================
// File: src/components/Profile.tsx
import React from "react";
import { useMsal } from "@azure/msal-react";

const Profile: React.FC = () => {
  const { accounts } = useMsal();
  const user = accounts[0];

  if (!user) return null;

  return (
    <div style={{ marginTop: "1rem" }}>
      <h3>Welcome, {user.name}</h3>
      <p>Email: {user.username}</p>
    </div>
  );
};

export default Profile;
