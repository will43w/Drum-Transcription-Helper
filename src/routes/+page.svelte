<script>
	import WaveformPlayer from "./WaveformPlayer.svelte";

	let src = $state("");
</script>

<div class="centered">
	<h1 class="header">
		Transcription Helper
	</h1>

	<WaveformPlayer>
	
	</WaveformPlayer>
</div>

<style>
	.header {
		font-family: system-ui;
	}

	.centered {
		display: flex;
		flex-direction: column;
		height: 100%;
		width: 80%;
		justify-content: center;
		gap: 0.5em;
		margin: 0 auto;
		font-family: system-ui;
	}
</style>