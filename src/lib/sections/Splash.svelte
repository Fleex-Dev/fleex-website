<script lang="ts">
	import LogoWithText from '$lib/assets/icons/logo-with-text.svelte';

	export let splash: HTMLElement;
</script>

<div bind:this={splash} class="fixed inset-0 z-10 flex">
	{#each Array(5) as _, i (i)}
		<div
			class="overlay relative flex h-full flex-1 items-center justify-center bg-[var(--color-key)]"
		>
			{#if i === 2}
				<div class="w-16 md:w-32">
					<svelte:component this={LogoWithText} />
				</div>
			{/if}
		</div>
	{/each}
</div>
