<script lang="ts">
	import { onMount } from 'svelte';
	import createGlobe from 'cobe';
	import { Spring } from 'svelte/motion';
	import { cn } from '$lib/utils';

	let x = new Spring({
		value: 0,
		stiffness: 0.04,
		damping: 0.4,
		precision: 0.005
	});

	export let globe: HTMLElement;
	export let scrollProgress = 0;

	let canvas: HTMLCanvasElement;
	let pointerInteracting: number | null = null;
	let phi = 0;
	let width = 0;

	// Resize canvas based on screen width
	const onResize = () => {
		width = canvas?.offsetWidth ?? 0;
	};

	// Main render loop
	const onRender = (state: any) => {
		if (!pointerInteracting) phi += 0.005;
		state.phi = phi + x.current.value;
		state.width = width * 2;
		state.height = width * 2;
		state.mapBrightness = 1.2 * (1 - scrollProgress);
	};

	onMount(() => {
		window.addEventListener('resize', onResize);
		onResize();

		const globe = createGlobe(canvas, {
			devicePixelRatio: 2,
			width: width * 2,
			height: width * 2,
			phi: 0,
			theta: 0.3,
			dark: 1,
			diffuse: 1.35,
			mapSamples: 500,
			mapBrightness: 1.2,
			mapBaseBrightness: 1.0,
			baseColor: [0.3, 0.3, 0.3],
			markerColor: [251 / 255, 100 / 255, 21 / 255],
			glowColor: [1, 1, 1],
			markers: [],
			onRender
		});

		return () => {
			window.removeEventListener('resize', onResize);
			globe?.destroy?.();
		};
	});
</script>

<main
	bind:this={globe}
	class={cn(
		'pointer-events-none fixed inset-0 z-0 mx-auto flex h-full w-full items-center justify-center overflow-hidden'
	)}>
	<div class="relative aspect-square w-full max-w-[600px]">
		<canvas
			class="h-full w-full cursor-grab touch-none [contain:layout_paint_size]"
			bind:this={canvas}></canvas>
	</div>
</main>
