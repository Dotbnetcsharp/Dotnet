const response = JSON.parse(responseBody);
vars.set("auth_token", response.access_token);

Bearer {{auth_token}}

const response = JSON.parse(response.body); // Parse the response body
vars.set("auth_token", response.access_token); // Set the auth_token variable
