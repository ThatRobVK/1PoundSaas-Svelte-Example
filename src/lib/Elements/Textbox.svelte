<script>
    import { Theme } from '$lib/Theme.js'
    import { Label, Input, Helper } from 'flowbite-svelte'
    import { v1 as uuidv1 } from 'uuid'

    export let labelText = '';
    export let placeholder = '';
    export let type = "text";
    export let value = '';
    export let disabled = false;

    export let validationMessage = "";

    // Shows a validation error message using the Helper object
    export function showError(message) {
        validationMessage = message;
    }

    // A unique ID for the input so multiple Textboxes can have their own Labels
    let unique_id = uuidv1();
</script>

<div class='mb-6'>
    <Label for='{unique_id}' class='block mb-2'>{labelText}</Label>
    <Input id="{unique_id}" {type} {disabled} placeholder="{placeholder}" bind:value={value} />
    {#if validationMessage}
        <Helper class='mt-2' color='{Theme.Error}'>{validationMessage}</Helper>
    {/if}
</div>