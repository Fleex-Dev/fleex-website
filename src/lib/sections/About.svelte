<script lang="ts">
	import Model from '$lib/components/Model.svelte';
	import * as THREE from 'three';

	export let aboutSection: HTMLElement;
	export let model: THREE.Object3D;
	export let aboutSlide: HTMLElement;
</script>

<section bind:this={aboutSection}>
	<div class="relative h-screen w-screen">
		<div
			bind:this={aboutSlide}
			class="absolute top-0 left-0 z-0 h-full w-full overflow-hidden transition-transform">
			<div
				class="slide h-screen w-full bg-cover bg-center"
				style="background-image: url('/images/1.jpg');">
			</div>
			<div
				class="slide h-screen w-full bg-cover bg-center"
				style="background-image: url('/images/2.jpg');">
			</div>
			<div
				class="slide h-screen w-full bg-cover bg-center"
				style="background-image: url('/images/3.jpg');">
			</div>
		</div>

		<!-- 모델: canvas는 z-index 낮게 -->
		<Model bind:model />
	</div>
</section>
