if (typeof responseBody !== "undefined") {
    const parsedResponse = JSON.parse(responseBody); // Parse the response body as JSON
    if (parsedResponse.token) {
        state.setVariable('authToken', parsedResponse.token); // Save the token
    } else {
        console.error('Token not found in response');
    }
} else {
    console.error('responseBody is undefined. Check Bruno’s documentation or response access settings.');
}
