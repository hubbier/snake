<script lang="ts">
	import Settings from './Settings.svelte';
	import Snake from './Snake.svelte';
    let { children } = $props();
</script>

{@render children()}