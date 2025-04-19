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
	let aboutSlide: HTMLElement;

	let scrollProgress = 0;
	let starsOpacity = 0;

	gsap.registerPlugin(ScrollTrigger);

	let lastRotation = 0;
	let currentSlide = 0; // 현재 보여지는 슬라이드 인덱스
	const totalSlides = 3; // 슬라이드 개수

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

		// Home 텍스트 애니메이션
		homeHeadline.forEach((span, i) => {
			timeline.fromTo(
				span,
				{ opacity: 0, y: 10, scale: 0.98 },
				{ opacity: 1, y: 0, scale: 1, duration: 0.6, ease: 'power3.out' },
				`afterSplash+=${i * 0.08}`
			);
		});

		// Globe 애니메이션
		timeline
			.fromTo(
				globe,
				{ opacity: 0, scale: 0 },
				{ opacity: 1, scale: 1, duration: 1, ease: 'power3.out' }
			)
			.to(
				{ val: starsOpacity },
				{
					val: 1,
					duration: 1,
					ease: 'power2.out',
					onUpdate() {
						starsOpacity = this.targets()[0].val;
					}
				},
				'afterGlobe'
			)
			.fromTo(
				nav,
				{ y: '-100%', opacity: 0 },
				{ y: '0%', opacity: 1, duration: 1, ease: 'power3.out' }
			)
			.fromTo(
				homeScrollIndicator,
				{ opacity: 0 },
				{ opacity: 1.0, duration: 1, ease: 'power2.out' },
				'<'
			)
			.fromTo(background, { height: '100vh' }, { height: '100%', duration: 1 }, '<');

		timeline.play();

		// Update for the home section scroll
		ScrollTrigger.create({
			trigger: homeSection,
			start: 'top top',
			end: 'bottom center',
			scrub: true,
			onUpdate: (self) => {
				const progress = self.progress;
				scrollProgress = progress;

				const scale = 1 + progress * 5;
				globe.style.transform = `scale(${scale})`;

				starsOpacity = 1 - progress;
				homeScrollIndicator.style.opacity = `${1 - progress}`;
			}
		});

		// Fade globe out when entering about section
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

		// About section scroll: Rotate model and handle background slide on full rotation
		ScrollTrigger.create({
			trigger: aboutSection,
			start: 'top top',
			scrub: true,
			pin: true,
			onUpdate: (self) => {
				const progress = self.progress;
				// Calculate the current rotation in radians (adjust factor as needed)
				const currentRotation = progress * Math.PI * 6;
				if (model) {
					model.rotation.y = currentRotation;
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
		<About bind:aboutSection bind:model bind:aboutSlide />
		<!-- 추가 콘텐츠: 예시로 긴 스크롤 섹션 -->
		<section class="relative h-[500vh] w-screen"></section>
	</div>
</div>
