<script>
    import { Button } from 'flowbite-svelte'
    import { Theme } from '$lib/Theme.js'

    export let text;
    export let outline = '';
</script>

<Button color="{Theme.Main}" {outline} on:click>{text}</Button>