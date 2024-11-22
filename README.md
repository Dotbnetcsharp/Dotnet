const response = JSON.parse(responseBody);
vars.set("auth_token", response.access_token);

Bearer {{auth_token}}
