<script lang="ts">
    let { src }  = $props();

    import RangeSlider from 'svelte-range-slider-pips'

    let time = $state(0);
    let duration = $state(0);
    let paused = $state(true);
    let timeValues = $state([0, 10, 20]);
    let loopStart = $derived(timeValues[0]);
    let loopEnd = $derived(timeValues[1]);
    let time2 = $derived(timeValues[2])

    function formatTime(timeInSeconds: number) {
		if (isNaN(timeInSeconds)) return '...';

		const minutes = Math.floor(timeInSeconds / 60);
		const seconds = Math.floor(timeInSeconds % 60);

		return `${minutes}:${seconds < 10 ? `0${seconds}` : seconds}`;
	}

    let currentTimeSliderHandler = (e) => {
        const div = e.currentTarget;
        
        function seek(e) {
            const { left, width } = div.getBoundingClientRect();

            let p = (e.clientX - left) / width;
            if (p < 0) {
                p = 0;
            }
            if (p > 1) {
                p = 1;
            }

            const value = p * duration;

            if (e.target.id === 'loopStart') {
                loopStart = value;
            } else if (e.target.id === 'loopEnd') {
                loopEnd = value;
            } else {
                time = value;
            }
        }

        seek(e);

        window.addEventListener('pointermove', seek);
        window.addEventListener('pointerup', () => {
            window.removeEventListener('pointermove', seek);
        }, {
            once: true
        });
    };

    let getLoopSliderHandler = (value) => {
        let loopSliderHandler = (e) => {
            const input = e.target;
            value = (input.value - input.min) / (input.max - input.min) * 100;
        };

        return loopSliderHandler;
    };

</script>

<div class={['player', { paused }]}>
    <audio
        {src}
        bind:currentTime={time}
        bind:duration
        bind:paused
        onended={() => {
            time = 0;
        }}
    ></audio>

    <button 
        class="play"
        aria-label={paused ? 'play' : 'pause'}
        onclick={() => paused = !paused}
    ></button>

    <div class="info">
        <div class="description">
            TODO: Description from URL
        </div>

        <div class="time">
            <span>{formatTime(time)}</span>
            <div
                class="currentTimeSlider"
                onpointerdown={(e) => {
                    const div = e.currentTarget;
                    
                    function seek(e) {
                        const { left, width } = div.getBoundingClientRect();

                        let p = (e.clientX - left) / width;
                        if (p < 0) {
                            p = 0;
                        }
                        if (p > 1) {
                            p = 1;
                        }

                        const value = p * duration;

                        if (e.target.id === 'loopStart') {
                            loopStart = value;
                        } else if (e.target.id === 'loopEnd') {
                            loopEnd = value;
                        } else {
                            time = value;
                        }
                    }

                    seek(e);

                    window.addEventListener('pointermove', seek);
                    window.addEventListener('pointerup', () => {
                        window.removeEventListener('pointermove', seek);
                    }, {
                        once: true
                    });
                }}
            >
            TODO SLIDER HERE

            </div>
        </div>
        <div>
            <span>{duration ? formatTime(duration) : '--:--'}</span>
        </div>
        <p>end time: {loopEnd}</p>

        <div class="slider-container">
            <RangeSlider 
                range 
                pushy
                min={0}
                max={duration} 
                values={[0, 10, 20]} />
        </div>
    </div>
</div>


<style>
    .slider-container {
        position: relative;
        width: 100%;
        padding: 0px 0;
    }

    /* Make progress indicator thumb look different */
    .slider-container :global(.range-slider__thumb:nth-child(3)) {
        background-color: #2196f3;
        width: 2px;
        height: 20px;
    }

    /* Optional: adjust other thumbs */
    .slider-container :global(.range-slider__thumb):not(:nth-child(3)) {
        background-color: black;
        width: 2px;
        height: 20px;
    }

	.player {
		display: grid;
		grid-template-columns: 2.5em 1fr;
		align-items: center;
		gap: 1em;
		padding: 0.5em 1em 0.5em 0.5em;
		border-radius: 2em;
		background: var(--bg-1);
		transition: filter 0.2s;
		color: var(--fg-3);
		user-select: none;
	}

	.player:not(.paused) {
		color: var(--fg-1);
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
		overflow: hidden;
	}

	.description {
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		line-height: 1.2;
	}

	.time {
		display: flex;
		align-items: center;
		gap: 0.5em;
	}

	.time span {
		font-size: 0.7em;
	}

	.currentTimeSlider {
		flex: 1;
		height: 0.5em;
		background: grey;
		border-radius: 0.5em;
		overflow: hidden;
	}

	.progress {
		width: calc(100 * var(--progress));
		height: 100%;
		background: blue;
	}

    .progress-indicator {
        position: absolute;
        top: 0;
        bottom: 0;
        left: calc(100 * var(--progress));
        width: 2px;
        background: #0f1114;
        z-index: 2;
    }

</style>




