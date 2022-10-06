<script>

    import Textbox from '$lib/Elements/Textbox.svelte'
    import Button from '$lib/Elements/Button.svelte'

    function sendVerificationCode() {
    }

</script>

To change your password, enter your registered e-mail address below. If your address is recognised, a verification code
will be e-mailed to you.
<Textbox id="email" type="email" labelText="E-mail" placeholder="bob@1poundsaas.com" />
<Button text="Send code"

<Textbox id="verificationCode" labelText="Verification code" placeholder="Check your e-mail" />
<Textbox id="password" type="password" labelText="New Password" placeholder="Your new password" />
<Button text="Change password" on:click={() => alert('clicked')} />
