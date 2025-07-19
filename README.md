
// src/components/Navbar.tsx
import React, { useEffect, useState } from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import "./Navbar.css"; // make sure this has your popup-avatar and styles

const Navbar: React.FC = () => {
  const { instance } = useMsal();
  const [userModalOpen, setUserModalOpen] = useState(false);
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [userName, setUserName] = useState("");
  const [userEmail, setUserEmail] = useState("");

  // Check for logged in account
  useEffect(() => {
    const accounts = instance.getAllAccounts();
    if (accounts.length > 0) {
      const account = accounts[0];
      setIsAuthenticated(true);
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
    instance.logoutPopup({ postLogoutRedirectUri: "/" });
    setIsAuthenticated(false);
    setUserName("");
    setUserEmail("");
    setUserModalOpen(false);
  };

  const toggleUserModal = () => {
    if (!isAuthenticated) {
      handleLogin();
    } else {
      setUserModalOpen((prev) => !prev);
    }
  };

  return (
    <div className="nav-user-wrapper">
      <button
        className="nav-user-btn dropdown-btn"
        title="My profile"
        type="button"
        onClick={toggleUserModal}
      >
        <span className="sr-only">My profile</span>
        <span className="nav-user-img">
          <img src="/path/to/user.png" alt="User" />
        </span>
      </button>

      {userModalOpen && isAuthenticated && (
        <div className="user-profile-popup">
          <div className="popup-header">
            <span className="popup-title">Personal</span>
            <button className="popup-signout" onClick={handleLogout}>
              Sign out
            </button>
          </div>
          <div className="popup-user-info">
            <div className="popup-avatar">{userName?.charAt(0)}</div>
            <div className="popup-details">
              <p className="popup-name">{userName}</p>
              <p className="popup-email">{userEmail}</p>
              <a href="#" className="popup-link">
                My Microsoft account
              </a>
            </div>
          </div>
          <div className="popup-status">
            <div className="status-info">
              <span className="status-dot"></span>
              <span>Available</span>
            </div>
            <button className="status-message">Set status message</button>
          </div>
          <div className="popup-footer">
            <span className="add-icon">+</span>
            <span>Add another account</span>
          </div>
        </div>
      )}
    </div>
  );
};

export default Navbar;
