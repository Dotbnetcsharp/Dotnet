npm install @azure/msal-browser @azure/msal-react
// src/authConfig.ts
import { Configuration } from "@azure/msal-browser";

export const msalConfig: Configuration = {
  auth: {
    clientId: process.env.REACT_APP_EAGLE_ID_CLIENT_ID!, // from .env
    authority: `https://login.microsoftonline.com/${process.env.REACT_APP_EAGLE_ID_TENANT_ID}`,
    redirectUri: process.env.REACT_APP_EAGLE_ID_REDIRECT_URI,
  },
  cache: {
    cacheLocation: "sessionStorage", // or localStorage
    storeAuthStateInCookie: false,
  },
};

export const loginRequest = {
  scopes: ["User.Read"], // or other scopes if needed
};





// src/index.tsx or App.tsx
import React from "react";
import ReactDOM from "react-dom";
import { PublicClientApplication } from "@azure/msal-browser";
import { MsalProvider } from "@azure/msal-react";
import App from "./App";
import { msalConfig } from "./authConfig";

const msalInstance = new PublicClientApplication(msalConfig);

ReactDOM.render(
  <React.StrictMode>
    <MsalProvider instance={msalInstance}>
      <App />
    </MsalProvider>
  </React.StrictMode>,
  document.getElementById("root")
);


// src/components/Login.tsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { InteractionType, InteractionRequiredAuthError } from "@azure/msal-browser";
import { loginRequest } from "../authConfig";

const Login = () => {
  const { instance, accounts } = useMsal();

  const handleLogin = async () => {
    try {
      const loginResponse = await instance.loginPopup(loginRequest); // or loginRedirect
      const account = loginResponse.account;
      const tokenResponse = await instance.acquireTokenSilent({
        ...loginRequest,
        account: account!,
      });

      const jwt = tokenResponse.accessToken;
      sessionStorage.setItem("jwt_token", jwt); // ✅ Store in session
      console.log("JWT Token: ", jwt);
    } catch (error) {
      if (error instanceof InteractionRequiredAuthError) {
        const response = await instance.acquireTokenPopup(loginRequest);
        sessionStorage.setItem("jwt_token", response.accessToken);
      } else {
        console.error("Login Error: ", error);
      }
    }
  };

  return <button onClick={handleLogin}>Login with Microsoft</button>;
};

export default Login;







