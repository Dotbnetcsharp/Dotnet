import React, { useEffect } from "react";
import { Routes, Route } from "react-router-dom";
import { useMsal } from "@azure/msal-react";
import Home from "./pages/Home";
import AuthCallback from "./pages/AuthCallback";

const App: React.FC = () => {
  const { instance, accounts } = useMsal();

  useEffect(() => {
    const request = {
      scopes: ["User.Read"], // Replace with your real scopes
      account: accounts[0],
    };

    const fetchToken = async () => {
      try {
        const response = await instance.acquireTokenSilent(request);
        sessionStorage.setItem("eagle-id-session.jwt", response.accessToken);
        console.log("✅ Token stored in sessionStorage");
      } catch (error) {
        console.error("❌ Failed to acquire token silently", error);
      }
    };

    if (accounts.length > 0) {
      fetchToken();
    }
  }, [accounts, instance]);

  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/auth" element={<AuthCallback />} />
    </Routes>
  );
};

export default App;
