<script>
	import AudioPlayer from "./AudioPlayer.svelte";
	import ProgressBar from "./ProgressBar.svelte";

	let time = $state(0);
	let duration = $state(0);
	let paused = $state(true);
	let loopStart = $state(0);
	let loopEnd = $state(10);
</script>
<h1>Welcome to SvelteKit</h1>

<AudioPlayer
	src={'https://sveltejs.github.io/assets/music/satie.mp3'}
	loopStart={10}
	loopEnd={15}
></AudioPlayer>

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