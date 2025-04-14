<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';
	import Splash from '$lib/sections/Splash.svelte';
	import Home from '$lib/sections/Home.svelte';
	import Nav from '$lib/components/Nav.svelte';
	import Stars from '$lib/components/Stars.svelte';
	import About from '$lib/sections/About.svelte';
	import * as THREE from 'three';

	let timeline = gsap.timeline({ paused: true });

	let splash: HTMLElement;
	let nav: HTMLElement;
	let background: HTMLElement;

	let homeSection: HTMLElement;
	let homeHeadline: HTMLElement[] = [];
	let homeScrollIndicator: HTMLElement;
	let globe: HTMLElement;

	let aboutSection: HTMLElement;
	let model: THREE.Object3D;

	let scrollProgress = 0;

	let starsOpacity = 0;

	gsap.registerPlugin(ScrollTrigger);

	onMount(() => {
		// Splash 애니메이션
		const splashOverlays = Array.from(splash!.querySelectorAll('.overlay')).reverse();
		splashOverlays.forEach((el) => {
			timeline.to(
				el,
				{
					y: '-100%',
					duration: 0.5,
					ease: 'power2.inOut'
				},
				`<=${0.2}`
			);
		});

		// Home 텍스트
		homeHeadline.forEach((span, i) => {
			timeline.fromTo(
				span,
				{ opacity: 0, y: 10, scale: 0.98 }, // y값 줄이고 약간 축소
				{ opacity: 1, y: 0, scale: 1, duration: 0.6, ease: 'power3.out' },
				`afterSplash+=${i * 0.08}`
			);
		});

		// Globe
		timeline
			.fromTo(
				globe,
				{
					opacity: 0,
					scale: 0
				},
				{
					opacity: 1,
					scale: 1,
					duration: 1,
					ease: 'power3.out'
				}
			)
			.to(
				{ val: starsOpacity },
				{
					val: 1,
					duration: 1,
					ease: 'power2.out',
					onUpdate() {
						starsOpacity = this.targets()[0].val; // 반응형 바인딩을 업데이트
					}
				},
				'afterGlobe' // ← 라벨로 연결
			)
			.fromTo(
				nav,
				{
					y: '-100%',
					opacity: 0
				},
				{
					y: '0%',
					opacity: 1,
					duration: 1,
					ease: 'power3.out'
				}
			)
			.fromTo(
				homeScrollIndicator,
				{ opacity: 0 },
				{ opacity: 1.0, duration: 1, ease: 'power2.out' },
				'<'
			)
			.fromTo(background, { height: '100vh' }, { height: '100%', duration: 1 }, '<');

		timeline.play();

		ScrollTrigger.create({
			trigger: homeSection, // 스크롤 기준이 되는 섹션
			start: 'top top',
			end: '+=500', // 1000px 스크롤 동안 애니메이션 진행
			scrub: true,
			onUpdate: (self) => {
				const progress = self.progress;
				scrollProgress = progress;

				const scale = 1 + progress * 5; // 스크롤이 진행될수록 5배까지 커짐
				globe.style.transform = `scale(${scale})`;

				starsOpacity = 1 - progress;
				homeScrollIndicator.style.opacity = `${1 - progress}`;

				if (progress >= 0.95) {
					gsap.to(globe, { opacity: 0, duration: 0.5, ease: 'power2.out' });
				} else {
					gsap.to(globe, { opacity: 1, duration: 0.5, ease: 'power2.out' });
				}
			}
		});
		ScrollTrigger.create({
			trigger: aboutSection,
			start: 'top center',
			end: 'bottom center',
			onEnter: () => {
				gsap.to(globe, { opacity: 0, duration: 0.5, ease: 'power2.out' });
			},
			onLeaveBack: () => {
				gsap.to(globe, { opacity: 1, duration: 0.5, ease: 'power2.out' });
			}
		});
		ScrollTrigger.create({
			trigger: aboutSection,
			start: 'top top',
			end: '+=3000',
			scrub: true,
			pin: true,
			onUpdate: (self) => {
				const progress = self.progress;
				if (model) {
					model.rotation.y = progress * Math.PI * 1; // 360도 회전
				}
			}
		});
	});
</script>

<div bind:this={background} class="no-scrollbar relative flex w-full overflow-hidden bg-black">
	<Stars bind:opacity={starsOpacity} />

	<!-- 콘텐츠 영역: 배경 위에 표시 -->
	<div class="relative bg-transparent">
		<Splash bind:splash />
		<Nav bind:nav />
		<Home
			bind:homeSection
			bind:homeHeadline
			bind:globe
			bind:homeScrollIndicator
			bind:scrollProgress />
		<About bind:aboutSection bind:model />
		<!-- 추가 콘텐츠: 예시로 긴 스크롤 섹션 -->
		<section class="relative h-[500vh] w-screen"></section>
	</div>
</div>
