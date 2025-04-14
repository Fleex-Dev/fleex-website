<script lang="ts">
	import LogoWithText from '$lib/assets/icons/logo-with-text.svelte';

	export let nav: HTMLElement;
</script>

<nav
	bind:this={nav}
	class="fixed top-0 left-0 z-50 w-full -translate-y-full bg-black/70 text-white opacity-0"
>
	<div class="mx-auto flex max-w-7xl items-center justify-between px-6 py-4">
		<div class="w-16 md:w-32">
			<svelte:component this={LogoWithText} />
		</div>
		<ul class="flex gap-6 text-sm uppercase">
			<li><a href="#home" class="hover:underline">Home</a></li>
			<li><a href="#about" class="hover:underline">About</a></li>
			<li><a href="#contact" class="hover:underline">Contact</a></li>
		</ul>
	</div>
</nav>
