<script>
    let {
        time=$bindable(0),
        paused=$bindable(true),
        duration,
        checkpoint
    } = $props();

	function format(time) {
		if (isNaN(time)) return '...';

		const minutes = Math.floor(time / 60);
		const seconds = Math.floor(time % 60);

		return `${minutes}:${seconds < 10 ? `0${seconds}` : seconds}`;
	}
</script>

<div class={['player', { paused }]}>
	<button
		class="play"
		aria-label={paused ? 'play' : 'pause'}
		onclick={() => paused = !paused}
	></button>

    <div class="info">
		<div class="time">
			<span>{format(time)}</span>
			<div
				class="slider"
				onpointerdown={e => {
					const div = e.currentTarget;

					function seek(e) {
						const { left, width } = div.getBoundingClientRect();

						let p = (e.clientX - left) / width;
						if (p < 0) p = 0;
						if (p > 1) p = 1;

						time = p * duration;
					}

					seek(e);

					window.addEventListener('pointermove', seek);
                    // window.addEventListener('keydown', seek); // TODO: Add keybindings for moving time

					window.addEventListener('pointerup', () => {
						window.removeEventListener('pointermove', seek);
                        // window.removeEventListener('keyup', seek) // TODO: Remove for moving time
					}, {
						once: true
					});
				}}
			>
				<div class="progress" style="--progress: {time / duration}%"></div>
                <div class="marker" style="--offset: {100 * checkpoint / duration}%"></div>
			</div>
			<span>{duration ? format(duration) : '--:--'}</span>
		</div>
	</div>
</div>

<style>
	.player {
		display: grid;
		grid-template-columns: 2.5em 1fr;
		align-items: center;
		gap: 1em;
		padding: 0.5em 1em 0.5em 0.5em;
		border-radius: 2em;
		background: white;
		transition: filter 0.2s;
        color: black;
		user-select: none;
        filter: drop-shadow(0.5em 0.5em 1em rgba(0,0,0,0.1));
	}

	button {
		width: 100%;
		aspect-ratio: 1;
		background-repeat: no-repeat;
		background-position: 50% 50%;
		border-radius: 50%;
	}

	[aria-label="pause"] {
		background-image: url(./pause.svg);
	}

	[aria-label="play"] {
		background-image: url(./play.svg);
	}

	.info {
		
	}

	.time {
		display: flex;
		align-items: center;
		gap: 0.5em;
	}

	.time span {
		font-size: 0.7em;
	}

	.slider {
		flex: 1;
		height: 0.5em;
		background: lightgrey;
		border-radius: 0.5em;
        align-items: center;
        position: relative;
        overflow: visible;
        overflow-y: visible;
	}

	.progress {
		width: calc(100 * var(--progress));
		height: 100%;
		background: #7b68ee;
        border-radius: 0.5em;
        flex: 1;
        position: absolute;
        z-index: 1;
        overflow: visible;
	}

    .marker {
        position: absolute;
        top: 50%;
        transform: translate(-50%, -50%); /* center horizontally & vertically */
        left: var(--offset);
        background: black;
        width: 6px;
        height: 1em;
        border-radius: 999px;
        z-index: 2;
    }
</style>
