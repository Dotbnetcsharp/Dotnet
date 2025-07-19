.nav-user-wrapper {
  position: relative;
}

.nav-user-img img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.user-profile-popup {
  position: absolute;
  top: 50px;
  right: 0;
  width: 300px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
  padding: 16px;
  z-index: 999;
}

.popup-user-info {
  display: flex;
  gap: 12px;
  align-items: center;
  margin-top: 12px;
  flex-wrap: wrap;
}

.popup-avatar {
  width: 62px;
  height: 62px;
  border-radius: 50%;
  background: #ddd;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 18px;
}

.popup-details {
  flex: 1;
  min-width: 0;
}

.popup-name {
  font-weight: bold;
  margin: 0;
}

.popup-email {
  margin: 0;
  color: #555;
  word-wrap: break-word;
  white-space: normal;
  font-size: 14px;
}

.popup-link {
  color: #0078d4;
  text-decoration: none;
  font-size: 13px;
}

.popup-signout,
.status-message {
  background: none;
  border: none;
  color: #0078d4;
  cursor: pointer;
  font-size: 14px;
}

.popup-header,
.popup-status,
.popup-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 16px;
}

.status-info {
  display: flex;
  align-items: center;
  gap: 6px;
}

.status-dot {
  width: 10px;
  height: 10px;
  background-color: green;
  border-radius: 50%;
}

.add-icon {
  font-size: 20px;
  margin-right: 6px;
}
