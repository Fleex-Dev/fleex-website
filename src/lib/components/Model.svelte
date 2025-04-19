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
		const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
		renderer.setSize(window.innerWidth, window.innerHeight);
		camera.position.z = 2;

		// WebGLRenderer의 배경을 투명하게 설정
		renderer.setClearColor(0x000000, 0);

		// 광원 설정
		const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
		scene.add(ambientLight);
		const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
		directionalLight.position.set(5, 10, 7.5);
		scene.add(directionalLight);

		// Draco loader 설정
		const dracoLoader = new DRACOLoader();
		dracoLoader.setDecoderPath('/draco/');
		const loader = new GLTFLoader();
		loader.setDRACOLoader(dracoLoader);

		loader.load('/models/iphone/iphone.glb', (gltf) => {
			model = gltf.scene;
			model.scale.set(5, 5, 5);
			model.rotation.set(3, 0.2, 10);
			scene.add(model);
		});

		// 화면 크기 변경 시 호출할 함수: 카메라와 렌더러 업데이트
		const onResize = () => {
			camera.aspect = window.innerWidth / window.innerHeight;
			camera.updateProjectionMatrix();
			renderer.setSize(window.innerWidth, window.innerHeight);
		};
		window.addEventListener('resize', onResize);

		// 애니메이션 루프
		const animate = () => {
			requestAnimationFrame(animate);
			renderer.render(scene, camera);
		};
		animate();
	});
</script>

<canvas bind:this={canvas} class="pointer-events-none absolute top-0 left-0 z-10 h-full w-full"
></canvas>
