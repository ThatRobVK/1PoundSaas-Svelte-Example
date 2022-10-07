<script>

    // External imports
    import { onMount, onDestroy } from 'svelte'
    import {Alert, Checkbox, Label, Spinner} from 'flowbite-svelte'

    // Custom imports
    import Textbox from './Elements/Textbox.svelte'
    import Button from './Elements/Button.svelte'
    import { Email } from './Stores.js';

    // Form fields and values
    let email = '';
    let password = '';
    let refreshToken = '';
    let rememberMe = true;
    let emailInput;
    let passwordInput;

    // When set, the user is shown an alert, when cleared the alert hides
    let errorMessage = '';

    // When true, the login form is disabled and a spinner is shown
    let loggingIn = false;

    // Subscribe to the e-mail in the store, so we know whether the user is logged in or not
    let storeEmail;
    const emailUnsubscribe = Email.subscribe(value => {
        storeEmail = value;
    });

    // Performs a login, either from the form or from a refresh token
    async function login() {
        // If no refresh token supplied, validate the form input
        if (!refreshToken && !validateInput()) {
            console.log('login :: No refresh token and input isn\'t valid, returning')
            return;
        }

        // Update UI for loggin in
        loggingIn = true;

        try {
            // Pass either the refresh token, or the username and password
            let requestBody = (refreshToken && refreshToken.length > 0) ?
                {email: 'abc', refreshToken: refreshToken} :
                {email: email, password: password, returnRefreshToken: rememberMe}

            console.log('login :: Sending login request: ' + JSON.stringify(requestBody));

            // Send login request
            let response = await fetch('https://dev1ps.tabletop-theatre.com/api/auth/login', {
                method: 'POST',
                body: JSON.stringify(requestBody)
            });

            if (response.status === 401) {
                // 401 means login failed
                errorMessage = 'Login failed. Check your email and password and try again.';
                return;
            }
            else if (!response.ok) {
                // Any other non-success message
                errorMessage = 'A server happened during login: ' + response.status + ': ' + response.statusText;
                return;
            }

            // Read the response
            let responseBody = await response.json();
            console.log('Response body:\n' + JSON.stringify(responseBody));

            // Take the middle section of the ID token, Base64 decode it and parse it as JSON
            let idToken = responseBody['idToken'];
            let decodedToken = JSON.parse(atob(idToken.split('.')[1]));
            Email.set(decodedToken['sub']);

            // Store the ID token in the session storage
            if (typeof(sessionStorage) !== 'undefined') {
                console.log('login :: Storing ID token in session storage.');
                sessionStorage.setItem('dev1ps-authorization', responseBody['idToken']);
            }

            // Store the refresh token in local storage
            if ('refreshToken' in responseBody && typeof(localStorage) !== 'undefined') {
                console.log('login :: Storing refresh token in local storage.');
                localStorage.setItem('dev1ps-refreshtoken', responseBody['refreshToken']);
            }

            errorMessage = '';
        }
        catch (err) {
            errorMessage = 'An error occurred during login: ' + err;
        }

        // Update UI
        loggingIn = false;
    }

    // Checks the user's form input
    function validateInput() {
        let passwordError = (password.length === 0) ? 'Password is required' : '';
        let emailError = !!email.match(/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/)
            ? '' : 'A valid e-mail address is required.';

        emailInput.showError(emailError);
        passwordInput.showError(passwordError);

        return emailError.length === 0 && passwordError.length === 0;
    }

    // Logs the user out by clearing all their local identity proofs
    function logout() {
        // Clear the store
        Email.set('');

        // Remove the ID token from session storage
        if (typeof(sessionStorage) !== 'undefined') {
            sessionStorage.removeItem('dev1ps-authorization');
        }

        // Remove the refresh token from local storage
        if (typeof(localStorage) !== 'undefined') {
            localStorage.removeItem('dev1ps-refreshtoken');
        }
    }

    // On mounting, log the user in automatically if there's a refresh token
    onMount(async () => {
        // Already logged in
        if (storeEmail.length > 0) {
            console.log('onMount :: Already logged in, returning');
            return;
        }

        // Get the refresh token from local storage, if it exists, call the login function
        if (typeof(localStorage) !== 'undefined') {
            console.log('onMount :: Getting refresh token');
            refreshToken = localStorage.getItem('dev1ps-refreshtoken');

            if (refreshToken) {
                console.log('onMount :: Logging in with refresh token');
                await login();
            }
        }
    });

    // Unsubscribe from the store on destroy to prevent memory leaks
    onDestroy(emailUnsubscribe);
</script>

{#if errorMessage.length > 0}
<Alert dismissable color="red"  on:close={() => errorMessage = ''}>{errorMessage}</Alert>
{/if}

{#if storeEmail && storeEmail.length > 0}
    <p>Logged in as {storeEmail}</p>
    <br/>
    <Button text="Logout" on:click={logout} />
{:else}
    <div class="space-y-6 relative">
        <br/>
        <Textbox bind:this={emailInput} type="email" labelText="E-mail" placeholder="bob@1poundsaas.com" bind:value={email} bind:disabled="{loggingIn}" />
        <Textbox bind:this={passwordInput} type="password" labelText="New Password" placeholder="Your password" bind:value={password} bind:disabled="{loggingIn}" />
        <p><Checkbox id="loginRememberMe" bind:checked={rememberMe} bind:disabled="{loggingIn}" /> <Label for="loginRememberMe" class="inline">Remember me</Label></p>
        <Button text="Login" on:click={login} bind:disabled="{loggingIn}" />  - <a href="/auth/reset-password">Forgot password</a> - <a href="/auth/register">Register</a>
        {#if loggingIn}
            <div class="absolute top-0 left-0 w-full h-full">
                <Spinner class="relative left-1/2 top-1/2" />
            </div>
        {/if}
    </div>
{/if}
{#if !loggingIn}
{/if}
