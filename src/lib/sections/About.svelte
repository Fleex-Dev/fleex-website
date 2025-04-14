<script lang="ts">
	import Model from '$lib/components/Model.svelte';
	import * as THREE from 'three';

	export let aboutSection: HTMLElement;
	export let model: THREE.Object3D;
</script>

<section bind:this={aboutSection} class="h-screen w-screen">
	<Model bind:model />
</section>
