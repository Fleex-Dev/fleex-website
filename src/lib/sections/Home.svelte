<script lang="ts">
	import gsap from 'gsap/dist/gsap';
	import IconifyIcon from '@iconify/svelte';
	import Globe from '$lib/components/Globe.svelte';
	import { onMount } from 'svelte';

	export let scrollProgress = 0;

	export let homeSection: HTMLElement;
	export let homeHeadline: HTMLElement[] = [];
	export let globe: HTMLElement;
	export let homeScrollIndicator: HTMLElement;

	let headline = 'BEYOND FITNESS';
	let chars = Array.from(headline);

	onMount(() => {
		const tl = gsap.timeline({ repeat: -1, repeatDelay: 0.5 });

		tl.to(homeScrollIndicator, {
			y: 14,
			duration: 0.5,
			ease: 'power2.out'
		}).to(homeScrollIndicator, {
			y: 0,
			duration: 0.7,
			ease: 'power2.inOut'
		});
	});
</script>

<section bind:this={homeSection}>
	<div class="relative flex flex-col">
		<div class="relative flex h-screen w-screen items-center justify-center overflow-hidden">
			<Globe bind:globe bind:scrollProgress />
			<p
				class="font-joyride-outline text-center text-[8vw] leading-none whitespace-nowrap text-white">
				{#each chars as char, i}
					<span
						class="inline-block translate-y-8 opacity-0"
						bind:this={homeHeadline[i]}
						data-char={char}>
						{char === ' ' ? '\u00A0' : char}
					</span>
				{/each}
			</p>
			<div
				bind:this={homeScrollIndicator}
				class="absolute bottom-0 mb-4 flex flex-col items-center">
				<p class="font-pretendard font-regular text-2xl text-white">Scroll Down</p>
				<IconifyIcon
					style="color: white"
					width="48.0"
					height="48.0"
					icon="material-symbols-light:keyboard-double-arrow-down" />
			</div>
		</div>
	</div>
</section>
