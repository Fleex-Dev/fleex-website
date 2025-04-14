<script lang="ts">
	import { onMount } from 'svelte';
	import * as THREE from 'three';

	let container: HTMLDivElement;
	export let opacity = 0;

	const STARS_COUNT = 350;
	const colors = ['#7EDB7C', '#A4E6A3', '#C3F3BD', '#6BCF9C', '#8FD7AA'];

	const stars: {
		pos: THREE.Vector3;
		len: number;
		speed: number;
		color: THREE.Color;
		rad: number;
	}[] = [];

	function r(min: number, max: number) {
		return min + Math.random() * (max - min);
	}

	function resetStar(star: (typeof stars)[0]) {
		if (r(0, 1) > 0.8) {
			star.pos.set(r(-10, -30), r(-5, 5), r(-6, 6));
			star.len = r(1.5, 15);
		} else {
			star.pos.set(r(-15, -45), r(-10.5, 1.5), r(-45, 30));
			star.len = r(2.5, 20);
		}
		star.speed = r(19.5, 42);
		star.rad = r(0.04, 0.07);
		star.color = new THREE.Color(colors[Math.floor(Math.random() * colors.length)])
			.convertSRGBToLinear()
			.multiplyScalar(1.3);
	}

	onMount(() => {
		// Three.js Scene, Camera, Renderer 설정
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(
			45,
			container.clientWidth / container.clientHeight,
			0.1,
			1000
		);
		camera.rotation.set(0, 45, 0);

		const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
		renderer.setSize(container.clientWidth, container.clientHeight);
		renderer.setPixelRatio(window.devicePixelRatio);
		container.appendChild(renderer.domElement);

		// 별을 그릴 PlaneGeometry, 텍스처, Material 생성
		const geometry = new THREE.PlaneGeometry(1, 0.05);
		const texture = new THREE.TextureLoader().load('/textures/star.png');
		const material = new THREE.MeshBasicMaterial({
			alphaMap: texture,
			transparent: true,
			side: THREE.DoubleSide,
			opacity: opacity,
			color: 0xffffff
		});

		// Instanced Mesh 생성
		const instancedMesh = new THREE.InstancedMesh(geometry, material, STARS_COUNT);
		instancedMesh.instanceMatrix.setUsage(THREE.DynamicDrawUsage);
		instancedMesh.instanceColor = new THREE.InstancedBufferAttribute(
			new Float32Array(STARS_COUNT * 3),
			3
		);
		scene.add(instancedMesh);

		const dummy = new THREE.Object3D();

		// 초기 별 상태 설정
		for (let i = 0; i < STARS_COUNT; i++) {
			const star = {
				pos: new THREE.Vector3(),
				len: 1,
				speed: 1,
				color: new THREE.Color(),
				rad: 0
			};
			resetStar(star);
			stars.push(star);
		}

		// 애니메이션 루프
		let prev = performance.now();
		function animate(now: number) {
			const delta = (now - prev) / 1000;
			prev = now;

			stars.forEach((star, i) => {
				star.pos.x += star.speed * delta;
				if (star.pos.x > 40) {
					resetStar(star);
				}

				dummy.position.copy(star.pos);
				dummy.scale.set(star.len, 1, 1);
				dummy.updateMatrix();
				instancedMesh.setMatrixAt(i, dummy.matrix);
				instancedMesh.setColorAt(i, star.color);
			});

			instancedMesh.instanceMatrix.needsUpdate = true;
			instancedMesh.instanceColor!.needsUpdate = true;
			material.opacity = opacity;

			renderer.render(scene, camera);
			requestAnimationFrame(animate);
		}
		animate(prev);

		// --- 크기 변경 감지를 위한 ResizeObserver 추가 ---
		const resizeObserver = new ResizeObserver((entries) => {
			// container의 크기가 변경될 때마다 renderer와 camera를 업데이트
			entries.forEach((entry) => {
				const { width, height } = entry.contentRect;
				renderer.setSize(width, height);
				camera.aspect = width / height;
				camera.updateProjectionMatrix();
			});
		});
		resizeObserver.observe(container);

		// 추가적으로 window의 resize 이벤트에도 대응 (두 방식이 동시에 동작해도 무방)
		function onWindowResize() {
			renderer.setSize(container.clientWidth, container.clientHeight);
			camera.aspect = container.clientWidth / container.clientHeight;
			camera.updateProjectionMatrix();
		}
		window.addEventListener('resize', onWindowResize);

		// 컴포넌트 언마운트시 cleanup 수행
		return () => {
			renderer.dispose();
			resizeObserver.disconnect();
			window.removeEventListener('resize', onWindowResize);
		};
	});
</script>

<div bind:this={container} class="pointer-events-none fixed inset-0 z-0 flex"></div>
