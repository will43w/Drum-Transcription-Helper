<script lang="ts">
    import { onMount, onDestroy } from 'svelte';
    import WaveSurfer from 'wavesurfer.js';
  
    let waveformContainer: HTMLDivElement;
    let audioFile: File | null = null;
    let wavesurfer: WaveSurfer;
    let isPlaying = false;
    let checkpoint: number | null = null;
    let playbackRate: number = 1.0;
  
    function handleFileChange(e: Event) {
        const target = e.target as HTMLInputElement;
        if (target.files?.[0]) {
            audioFile = target.files[0];
            const reader = new FileReader();
            reader.onload = () => {
                wavesurfer.loadBlob(new Blob([reader.result!]));
            };
            reader.readAsArrayBuffer(audioFile);
        }
    }
  
    function togglePlay() {
        isPlaying = !isPlaying;
        wavesurfer.playPause();
    }
  
    function setCheckpoint() {
        checkpoint = wavesurfer.getCurrentTime();
    }
  
    function returnToCheckpoint() {
        if (checkpoint !== null) {
            wavesurfer.setTime(checkpoint);
        }
    }

    function updatePlaybackRate(e: Event) {
        const target = e.target as HTMLInputElement;
        playbackRate = parseFloat(target.value);
        wavesurfer.setPlaybackRate(playbackRate);
    }
  
    function handleKeyDown(e: KeyboardEvent) {
        if (!waveformContainer.contains(document.activeElement)) return;
  
        switch (e.key) {
            case ' ':
                e.preventDefault();
                togglePlay();
                break;
            case 'ArrowRight':
                wavesurfer.skip(1);
                break;
            case 'ArrowLeft':
                wavesurfer.skip(-1);
                break;
            case 's':
                setCheckpoint();
                break;
            case 'r':
                returnToCheckpoint();
                break;
      }
    }
  
    onMount(() => {
        wavesurfer = WaveSurfer.create({
            container: waveformContainer,
            waveColor: '#999',
            progressColor: '#7b68ee',
            height: 50,
            cursorColor: 'black'
      });
  
      document.addEventListener('keydown', handleKeyDown);
    });
  
    // onDestroy(() => {
    //     document.removeEventListener('keydown', handleKeyDown);
    //     wavesurfer.destroy();
    // });
  
    function getCheckpointLeft(): string {
        if (!wavesurfer || checkpoint === null) return '0%';
        const duration = wavesurfer.getDuration();
        return duration ? `${(checkpoint / duration) * 100}%` : '0%';
    }
</script>
  
<style>
    .file-upload {
        border-radius: 10px;
    }

    .column-container {
        margin-top: 1rem;
        flex-direction: row;
        display: flex;
        align-items: center;
    }

    .row-container {
        margin-top: 1rem;
        display: flex;
        align-items: center;
    }
  
    .waveform-wrapper {
        flex: 1;
        margin-left: 1rem;
        position: relative;
        outline: none;
    }
  
    .checkpoint-line {
        position: absolute;
        width: 1px;
        background-color: red;
        top: 0;
        bottom: 10px;
        transform: translateX(-50%);
        z-index: 3;
    }
  
    .checkpoint-marker {
        position: absolute;
        width: 0;
        height: 0;
        border-left: 7px solid transparent;
        border-right: 7px solid transparent;
        border-top: 10px solid red;
        transform: translateX(-50%);
        z-index: 3;
    }

    button {
		width: 5%;
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

    .speed-slider {
        margin-top: 2rem;
        width: 100%;
        max-width: 400px;
    }

    .slider-label {
        font-family: system-ui;
        font-weight: 600;
        margin-bottom: 0.5rem;
        color: #444;
    }

    .slider-wrapper {
        position: relative;
        width: 100%;
        height: 40px;
    }

    .slider-tooltip {
        position: absolute;
        top: 30px;
        transform: translateX(-50%);
        background: #7b68ee;
        color: white;
        padding: 4px 8px;
        font-size: 0.8rem;
        border-radius: 4px;
        white-space: nowrap;
        pointer-events: none;
    }

    input[type="range"] {
        -webkit-appearance: none;
        width: 100%;
        height: 10px;
        background: linear-gradient(to right, #7b68ee, #7b68ee);
        border-radius: 5px;
        outline: none;
        transition: background 0.3s ease;
        cursor: pointer;
    }

    input[type="range"]::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        width: 20px;
        height: 20px;
        background: white;
        border: 2px solid '#7b68ee';
        border-radius: 50%;
        box-shadow: 0 0 5px #7b68ee;
        transition: transform 0.2s ease;
    }

    input[type="range"]::-webkit-slider-thumb:hover {
        transform: scale(1.2);
    }

    input[type="range"]::-moz-range-thumb {
        width: 20px;
        height: 20px;
        background: white;
        border: 2px solid '#7b68ee';
        border-radius: 50%;
        box-shadow: 0 0 5px black;
        transition: transform 0.2s ease;
    }

    input[type="range"]::-moz-range-thumb:hover {
        transform: scale(1.2);
    }
</style>
  
<div>
    <input type="file" accept="audio/*" onchange={handleFileChange} />
    <div class="row-container">
        <button
            class="play"
            aria-label={isPlaying ? "pause" : "play"}
            onclick={togglePlay}
            disabled={audioFile === null}
	    ></button>
        <div
            class="waveform-wrapper"
            tabindex="0"
            bind:this={waveformContainer}
        >
        {#if checkpoint !== null && wavesurfer}
        <div
            class="checkpoint-line"
            style="left: {getCheckpointLeft()};"
        ></div>
        <div
            class="checkpoint-marker"
            style="left: calc({getCheckpointLeft()});"
        ></div>
        {/if}
        </div>
    </div>
    <div class="speed-slider">
        <div class="slider-label">
          Playback Speed
        </div>
        <div class="slider-wrapper">
          <div class="slider-tooltip" style="left: {((playbackRate - 0.5) / 1.5) * 100}%">
            {playbackRate.toFixed(2)}x
          </div>
          <input
            type="range"
            min="0.5"
            max="2"
            step="0.05"
            bind:value={playbackRate}
            oninput={updatePlaybackRate}
          />
        </div>
      </div>
      
</div>
  