<script>
    import ProgressBar from "./ProgressBar.svelte";

    let {
        src
    } = $props();

    let time = $state(0);
    let checkpoint = $state(0);
    let duration = $state(0);
    let paused = $state(true);

    function onkeydown(e) {
        switch (e.key) {
            case 'ArrowRight':
                time += 0.2;
                break;

            case 'ArrowLeft':
                time -= 0.2;
                break;

            case ' ':
                paused = !$state.snapshot(paused);
                break;

            case 'r':
                time = $state.snapshot(checkpoint);
                break;

            case 's':
                checkpoint = $state.snapshot(time);
                break;
        }
    };
</script>

<svelte:window {onkeydown} />

<div >
    <audio
        {src}
        bind:currentTime={time}
        bind:duration
        bind:paused
    ></audio>

    <ProgressBar
        bind:time
        bind:paused
        {duration}
        {checkpoint}
    ></ProgressBar>
</div>
