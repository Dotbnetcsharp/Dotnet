
if ($response.json.token) {
    state.setVariable('authToken', $response.json.token); // Save the token to a state variable
} else {
    console.error('Token not found in response');
}
