<script>
	import MediaViewer from "./MediaViewer.svelte";
	import Slider from "./Slider.svelte";

</script>
<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>
<div class="centered">
    <MediaViewer src={'https://sveltejs.github.io/assets/music/strauss.mp3'} />
</div>
<div>
    <Slider />
</div>

<style>
	.centered {
		display: flex;
		flex-direction: column;
		height: 100%;
		justify-content: center;
		gap: 0.5em;
		max-width: 40em;
		margin: 0 auto;
	}
</style>