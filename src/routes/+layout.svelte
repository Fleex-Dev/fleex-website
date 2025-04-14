<script lang="ts">
	let { children } = $props();
	import { onMount } from 'svelte';
	import Lenis from 'lenis';
	import '../app.css';
	import '../styles/fonts.css';
	import '../styles/icons.css';
	import '../styles/style.css';

	onMount(() => {
		// Ensure this only runs on client
		const lenis = new Lenis({
			autoRaf: true
		});
	});
</script>

{@render children()}
