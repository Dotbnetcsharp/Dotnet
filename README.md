const response = JSON.parse(responseBody);
vars.set("auth_token", response.access_token);

Bearer {{auth_token}}

const response = JSON.parse(response.body); // Parse the response body
vars.set("auth_token", response.access_token); // Set the auth_token variable

const responseBody = response.body; // Get the raw response body
const responseJson = JSON.parse(responseBody); // Parse it as JSON
vars.set("auth_token", responseJson.access_token); // Save the access token to a variable


...
const responseJson = JSON.parse(Response.body); // Parse the response body
vars.set("auth_token", responseJson.access_token); // Save the access token to a variable


export const afterRequest = ({ response }, state) => {
  const token = response.json().token; // Adjust 'token' based on your response structure
  state.setVariable('authToken', token); // Save token to a state variable
};

const responseJson = response.json();
if (responseJson.token) {
    state.setVariable('authToken', responseJson.token); // Store the token in a state variable
} else {
    console.error('Token not found in response');
}
