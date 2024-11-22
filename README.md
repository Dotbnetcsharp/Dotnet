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


const parsedResponse = JSON.parse(this.responseBody); // Parse the response body
if (parsedResponse.access_token) {
    // Store the token in the 'authToken' state variable
    state.setVariable('authToken', parsedResponse.access_token);
    
    // Log the token value to confirm it is set correctly
    console.log('Auth Token:', state.getVariable('authToken'));  // This will show the token value in the console
} else {
    console.error('Access token not found in response');
}


try {
    // Check if the response body exists
    if (this.responseBody) {
        console.log('Raw Response Body:', this.responseBody);

        // Parse the response body to get the access_token
        const parsedResponse = JSON.parse(this.responseBody);
        
        // Check if the access_token exists in the response
        if (parsedResponse.access_token) {
            // Store the token in the state variable
            state.setVariable('authToken', parsedResponse.access_token);
            console.log('Auth Token:', state.getVariable('authToken'));  // Log the token for verification
        } else {
            console.error('Access token not found in response');
        }
    } else {
        console.error('Response body is empty or undefined');
    }
} catch (error) {
    console.error('Error parsing response:', error.message);
}



try {
    if (this.responseBody) {
        const parsedResponse = JSON.parse(this.responseBody);
        if (parsedResponse.access_token) {
            state.setVariable('authToken', parsedResponse.access_token);
            console.log('Auth Token:', state.getVariable('authToken'));
        } else {
            console.error('Access token not found in response');
        }
    } else {
        console.error('Response body is empty or undefined');
    }
} catch (error) {
    console.error('Error parsing response:', error.message);
    console.log('Full Response:', this);  // Log the full response object for further inspection
}
