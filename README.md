// src/components/SsoPopupPanel.tsx
import React, { useEffect, useState } from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";

const SsoPopupPanel = () => {
  const { instance } = useMsal();
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [userName, setUserName] = useState("");
  const [userEmail, setUserEmail] = useState("");

  useEffect(() => {
    const currentAccounts = instance.getAllAccounts();
    if (currentAccounts.length > 0) {
      setIsAuthenticated(true);
      const account = currentAccounts[0];
      setUserName(account.name || "");
      setUserEmail(account.username || "");
    }
  }, [instance]);

  const handleLogin = async () => {
    try {
      const loginResponse = await instance.loginPopup(loginRequest);
      const account = loginResponse.account;

      const tokenResponse = await instance.acquireTokenSilent({
        ...loginRequest,
        account,
      });

      sessionStorage.setItem("jwt_token", tokenResponse.accessToken);

      setIsAuthenticated(true);
      setUserName(account?.name || "");
      setUserEmail(account?.username || "");
    } catch (err) {
      console.error("Login failed:", err);
    }
  };

  const handleLogout = () => {
    sessionStorage.clear();
    instance.logoutPopup({
      postLogoutRedirectUri: "/",
    });
    setIsAuthenticated(false);
    setUserName("");
    setUserEmail("");
  };

  return (
    <div style={{ position: "relative", padding: "1rem" }}>
      {!isAuthenticated ? (
        <button onClick={handleLogin}>Login with Microsoft</button>
      ) : (
        <div
          style={{
            position: "absolute",
            top: "2.5rem",
            right: "0",
            background: "#f9f9f9",
            border: "1px solid #ccc",
            padding: "1rem",
            borderRadius: "8px",
            boxShadow: "0px 4px 10px rgba(0,0,0,0.1)",
            zIndex: 10,
          }}
        >
          <div style={{ marginBottom: "0.5rem" }}>
            👤 <strong>{userName}</strong>
            <br />
            📧 {userEmail}
          </div>
          <button onClick={handleLogout}>Logout</button>
        </div>
      )}
    </div>
  );
};

export default SsoPopupPanel;

// src/App.tsx
import React from "react";
import SsoPopupPanel from "./components/SsoPopupPanel";

function App() {
  return (
    <div style={{ padding: "2rem" }}>
      <h2>🔐 Microsoft SSO Login Example</h2>
      <SsoPopupPanel />
    </div>
  );
}

export default App;

