<script>
    import ProgressBar from "./ProgressBar.svelte";

    let {
        src,
        loopStart,
        loopEnd
    } = $props();

    let time = $state(0);
    let duration = $state(0);
    let paused = $state(true);

    $effect(() => {
        const id = setInterval(() => {
            if (time > loopEnd) {
                time = loopStart;
            }
        }, 3)

        return () => {
            clearInterval(id);
        }
    });
</script>

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
    {loopStart}
    {loopEnd}
>
</ProgressBar>
