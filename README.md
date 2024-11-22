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


const parsedResponse = JSON.parse(responseBody); // Parse the response body
if (parsedResponse.access_token) {
    state.setVariable('authToken', parsedResponse.access_token); // Save the access_token
} else {
    console.error('Access token not found in response');
}


const parsedResponse = JSON.parse(this.responseBody); // Use 'this.responseBody' to access the response body
if (parsedResponse.access_token) {
    state.setVariable('authToken', parsedResponse.access_token); // Save the access_token
} else {
    console.error('Access token not found in response');
}


try {
    const parsedResponse = JSON.parse(this.responseBody); // Parse the response body safely
    if (parsedResponse && parsedResponse.access_token) {
        state.setVariable('authToken', parsedResponse.access_token); // Save the access_token to the state variable
    } else {
        console.error('Access token not found in response');
    }
} catch (error) {
    console.error('Error parsing response body:', error);
}

Authorization

Value: Bearer {{authToken}}
