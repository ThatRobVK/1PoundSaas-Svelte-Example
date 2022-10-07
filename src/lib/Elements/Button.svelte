<script>
    import { Button } from 'flowbite-svelte'
    import { Theme } from '$lib/Theme.js'

    export let text;
    export let outline = '';
    export let color = Theme.Main;
    export let disabled = false;
</script>

<Button color="{color}" {outline} {disabled} on:click>{text}</Button>