<script lang="ts">
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js';

	export let model: THREE.Object3D;

	let canvas: HTMLCanvasElement;

	onMount(() => {
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(
			75,
			window.innerWidth / window.innerHeight,
			0.1,
			1000
		);
		const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true }); // alpha: true로 배경을 투명하게 설정
		renderer.setSize(window.innerWidth, window.innerHeight);
		camera.position.z = 2;

		// WebGLRenderer의 배경을 투명하게 설정
		renderer.setClearColor(0x000000, 0); // 첫 번째 인자는 색상, 두 번째 인자는 투명도(0이면 투명)

		// 광원 설정
		const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
		scene.add(ambientLight);

		const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
		directionalLight.position.set(5, 10, 7.5);
		scene.add(directionalLight);

		// Draco loader
		const dracoLoader = new DRACOLoader();
		dracoLoader.setDecoderPath('/draco/');
		const loader = new GLTFLoader();
		loader.setDRACOLoader(dracoLoader);

		loader.load('/models/iphone/iphone.glb', (gltf) => {
			model = gltf.scene;
			model.scale.set(10, 10, 10);
			model.rotation.set(3, 0.2, 10);
			scene.add(model);
		});

		const animate = () => {
			requestAnimationFrame(animate);

			// 모델 회전 로직은 삭제됨

			renderer.render(scene, camera);
		};
		animate();
	});
</script>

<canvas bind:this={canvas} class="z-1 h-screen w-screen bg-transparent"></canvas>
