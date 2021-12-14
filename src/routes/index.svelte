<script context="module">
	export async function load({ page }) {
		const url = `https://dog.ceo/api/breeds/image/random/15`;
		const res = await fetch(url);
		const data = await res.json();

		const loadedDogs = data.message.map((data, idx) => {
			return {
				url: data,
				id: idx + 1,
				description: `Block ${idx + 1}`
			};
		});

		return {
			props: { dogs: loadedDogs }
		};
	}
</script>

<script>
	export let dogs;
	console.log(dogs);

	import { onMount } from 'svelte';
	import '../app.css';

	let Home;

	const sleep = (ms) => new Promise((f) => setTimeout(f, ms));

	onMount(async () => {
		await sleep(1000); // simulate network delay
		Home = (await import('../lib/containers/home.svelte')).default;
	});
</script>

<svelte:component this={Home} {dogs}>
	<p>some slotted content</p>
</svelte:component>
