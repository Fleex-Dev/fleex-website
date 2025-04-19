<script lang="ts">
	import Stars from '$lib/components/Stars.svelte';

	export let starsOpacity: number;
</script>

<div class="no-scrollbar z-0 flex h-screen w-screen flex-col items-center justify-start bg-black">
	<Stars bind:opacity={starsOpacity} />
	<div class="absolute h-screen w-screen">
		<slot />
	</div>
</div>
