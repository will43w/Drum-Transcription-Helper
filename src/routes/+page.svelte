<script>
	import AudioPlayer from "./AudioPlayer.svelte";
	import MediaInput from "./AudioInput.svelte";
	import AudioInput from "./AudioInput.svelte";

	let src = $state("");
</script>
<h1>Welcome to SvelteKit</h1>

<AudioInput 
	bind:src={src}
></AudioInput>

<AudioPlayer
	{src}
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