// src/pages/AuthCallback.tsx
import React, { useEffect } from "react";
import { useMsal } from "@azure/msal-react";
import { InteractionStatus } from "@azure/msal-browser";
import { useNavigate } from "react-router-dom";

const AuthCallback: React.FC = () => {
  const { instance, inProgress } = useMsal();
  const navigate = useNavigate();

  useEffect(() => {
    const handleRedirect = async () => {
      if (inProgress === InteractionStatus.None) {
        try {
          const accounts = instance.getAllAccounts();
          if (accounts.length > 0) {
            const request = {
              account: accounts[0],
              scopes: ["User.Read"], // or your required scopes
            };

            const response = await instance.acquireTokenSilent(request);
            
            // ✅ Save token in sessionStorage
            sessionStorage.setItem("accessToken", response.accessToken);

            console.log("Access Token:", response.accessToken);
            navigate("/"); // redirect to home
          }
        } catch (error) {
          console.error("Silent token acquisition failed:", error);
        }
      }
    };

    handleRedirect();
  }, [inProgress, instance, navigate]);

  return <div>Signing in...</div>;
};

export default AuthCallback;
