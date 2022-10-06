<script>

    import Textbox from './Elements/Textbox.svelte'
    import Button from './Elements/Button.svelte'
    import {Alert, Toast} from 'flowbite-svelte'

    let email = '';
    let password = '';
    let emailInput;
    let passwordInput;

    let errorMessage = '';

    async function login() {
        if (!validateInput()) return;

        try {
            // Send login request
            let response = await fetch('https://dev1ps.tabletop-theatre.com/api/auth/login', {
                method: 'POST',
                body: JSON.stringify({email: email, password: password})
            });

            // 401 means login failed
            if (response.status === 401) {
                errorMessage = 'Login failed. Check your email and password and try again.';
                return;
            }
            else if (!response.ok) {
                errorMessage = 'A server happened during login: ' + response.status + ': ' + response.statusText;
                return;
            }

            // Read the response
            let responseBody = await response.json();
            if (typeof(sessionStorage) !== 'undefined') {
                sessionStorage.setItem('dev1ps-authorization', responseBody['idToken']);
            }

            if ('refreshToken' in responseBody && typeof(localStorage) !== 'undefined') {
                localStorage.setItem('dev1ps-refreshtoken', responseBody['refreshToken']);
            }
        }
        catch (err) {
            errorMessage = 'An error occurred during login: ' + err;
        }
    }

    function validateInput() {
        let passwordError = (password.length === 0) ? 'Password is required' : '';
        let emailError = !!email.match(/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/)
            ? '' : 'A valid e-mail address is required.';

        emailInput.showError(emailError);
        passwordInput.showError(passwordError);

        return emailError.length === 0 && passwordError.length === 0;
    }

</script>

{#if errorMessage.length > 0}
<Alert dismissable color="red"  on:close={() => errorMessage = ''}>{errorMessage}</Alert>
{/if}

<Textbox bind:this={emailInput} type="email" labelText="E-mail" placeholder="bob@1poundsaas.com" bind:value={email} />
<Textbox bind:this={passwordInput} type="password" labelText="New Password" placeholder="Your password" bind:value={password} />

<Button text="Login" on:click={login} />  - <a href="/auth/reset-password">Forgot password</a> - <a href="/auth/register">Register</a>
