const responseBody = JSON.parse(responseBody); // Parse the raw response body
if (responseBody.token) {
    state.setVariable('authToken', responseBody.token); // Save the token in a state variable
} else {
    console.error('Token not found in response');
}
