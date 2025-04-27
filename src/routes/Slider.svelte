<script lang="ts">
    // Bind loopStart, loopEnd, and currentTime - see https://svelte.dev/docs/svelte/$bindable
    // Duration should not be mutable in in this slider progress bar child
    // let {
    //     duration,
    //     loopStart,
    //     loopEnd,
    //     currentTime
    // } = $props();

    let iconX = 0;
    let dragging = false;
    let bindKeys = false;

    function handleKeyBindOnBubble(event) {
        window.addEventListener('keydown', handleKeyboardEvent)
    }

    function handleKeyUnbindOnCapture(event) {
        if (event.target.id === 'axis') {
            return;
        }
        window.removeEventListener('keydown', handleKeyboardEvent)
    }
  
    function handleMouseDown(event: MouseEvent) {
        dragging = true;
        window.addEventListener('mousemove', handleMouseMove);
        window.addEventListener('mouseup', handleMouseUp);
    }
  
    function handleMouseMove(event: MouseEvent) {
        if (!dragging) return;
    
        const axis = document.getElementById('axis');
        const icon = document.getElementById('icon');
        if (!axis || !icon) return;
    
        const axisRect = axis.getBoundingClientRect();
        const iconWidth = icon.offsetWidth;
        const maxX = axisRect.width - iconWidth;
        let newX = event.clientX - axisRect.left - iconWidth / 2;
    
        // Clamp to bounds
        iconX = Math.max(0, Math.min(newX, maxX));
    }
  
    function handleMouseUp() {
        dragging = false;
        window.removeEventListener('mousemove', handleMouseMove);
        window.removeEventListener('mouseup', handleMouseUp);
    }

    function handleKeyboardEvent(event: KeyboardEvent) {
        if (event.key === 'ArrowLeft') {
            iconX = Math.max(0, iconX - 5);
        }

        if (event.key === 'ArrowRight') {
            const axis = document.getElementById('axis');
            const icon = document.getElementById('icon');
            if (!axis || !icon) return;

            const axisRect = axis.getBoundingClientRect();
            const iconWidth = icon.offsetWidth;
            const maxX = axisRect.width - iconWidth;
            iconX = Math.min(maxX, iconX + 5);
        }
    }
</script>
  
<div 
    id="axis" 
    class="axis"
    onmousedown={handleKeyBindOnBubble}
    onmousedowncapture={handleKeyUnbindOnCapture}
>
    <div
        id="icon"
        class="icon"
        style="left: {iconX}px"
        onmousedown={handleMouseDown}
    ></div>
</div>
  
<style>
    .axis {
        display: flex;
        align-items: center;
        background-color: #ccc;
        height: 10px;
        width: 600px;
        border-radius: 3px;
        position: relative;
    }
  
    .icon {
        background-color: #444;
        width: 2px; /* Fixed width */
        height: 200%;
        border-radius: 50%;
        position: relative;
        cursor: pointer;
    }
</style>