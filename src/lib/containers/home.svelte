<script>
	//get data from parent component
	export let dogs;

	import 'locomotive-scroll/src/locomotive-scroll.scss';
	import { onMount } from 'svelte';

	import GridItem from '../components/GridItem.svelte';

	// import dogs from '../../data';

	import imagesLoaded from 'imagesloaded';

	//clamp the value to keep skewing smooth
	const clamp = (value, min, max) => (value <= min ? min : value >= max ? max : value);

	const preloadImages = (selector) => {
		return new Promise((resolve) => {
			imagesLoaded(document.querySelectorAll(selector), { background: true }, resolve);
		});
	};

	//Locomotive Scroll Refs
	let scroll = {
		cache: 0,
		current: 0
	};
	let reference;
	let leftColumnRef;
	let middleColumnRef;
	let rightColumnRef;

	//Split the data in 3
	const leftChunk = [...dogs].splice(0, 5);
	const middleChunk = [...dogs].splice(5, 5);
	const rightChunk = [...dogs].splice(10, 5);

	//Initialize everything on component mount
	onMount(async () => {
		//We need to wait for the document to load in order to import the scroll library
		const module = await import('locomotive-scroll');
		const LocomotiveScroll = module.default;

		const scrollElement = new LocomotiveScroll({
			el: reference,
			smooth: true,
			smartphone: {
				smooth: true
			},
			getDirection: true,
			getSpeed: true,
			lerp: 0.1
		});

		scrollElement.on('scroll', (obj) => {
			// Find distance between scroll updates
			scroll.current = obj.scroll.y;
			const distance = scroll.current - scroll.cache;
			scroll.cache = scroll.current;

			leftColumnRef.style.transform = `skewY(${clamp(distance, -10, 10)}deg)`;
			rightColumnRef.style.transform = `skewY(${clamp(distance, -10, 10)}deg)`;
			middleColumnRef.style.transform = `skewY(${clamp(-distance, -10, 10)}deg)`;
		});

		// Preload images
		Promise.all([preloadImages('.grid-item-media')]).then(() => {
			scrollElement.update();
		});

		// scrollElement.update();
	});
</script>

<!-- returning property data into your component -->

<svelte:head>
	<title>Home</title>
</svelte:head>

<div class="main-container" id="main-container" data-scroll-container bind:this={reference}>
	<div class="grid-wrap">
		<div class="left-column" bind:this={leftColumnRef}>
			{#each leftChunk as { url, description }, i}
				<GridItem {url} {description} index={i} />
			{/each}
		</div>
		<div class="middle-column" data-scroll data-scroll-speed="-20">
			<div bind:this={middleColumnRef}>
				{#each middleChunk as { url, description }, i}
					<GridItem {url} {description} index={i} />
				{/each}
			</div>
		</div>
		<div class="right-column" bind:this={rightColumnRef}>
			{#each rightChunk as { url, description }, i}
				<GridItem {url} {description} index={i} />
			{/each}
		</div>
	</div>
</div>

<style lang="scss">
	.grid-wrap {
		display: grid;
		grid-template-columns: repeat(3, 20%);
		gap: 5%;
		// width: 1400px;
		justify-content: center;
		margin: 0 auto;
	}
</style>
