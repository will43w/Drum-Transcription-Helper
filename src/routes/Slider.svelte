<script lang="ts">
    import { onMount } from 'svelte';
  
    let axis: HTMLDivElement;
    let icon: HTMLDivElement;
  
    let iconX = 0;
    let dragging = false;
  
    let axisWidth = 0;
    let iconWidth = 0;
  
    onMount(() => {
        axisWidth = axis.offsetWidth;
        iconWidth = icon.offsetWidth;
    });
  
    function handleMouseDown(event: MouseEvent) {
        dragging = true;
        window.addEventListener('mousemove', handleMouseMove);
        window.addEventListener('mouseup', handleMouseUp);
    }
  
    function handleMouseMove(event: MouseEvent) {
        if (!dragging) return;

        const axisLeft = axis.getBoundingClientRect().left;
        let newX = event.clientX - axisLeft - iconWidth / 2;

        // Clamp position within bounds
        newX = Math.max(0, Math.min(newX, axisWidth - iconWidth));
        iconX = newX;
    }
  
    function handleMouseUp() {
        dragging = false;
        window.removeEventListener('mousemove', handleMouseMove);
        window.removeEventListener('mouseup', handleMouseUp);
    }
</script>
  
  <style>
    .axis {
        background-color: #ccc;
        height: 20px;
        border-radius: 10px;
        position: relative;
        width: 100%;
    }

    .icon {
        background-color: #444;
        width: 10px;
        height: 30px;
        border-radius: 50%;
        position: absolute;
        top: -5px;
        cursor: pointer;
    }
  </style>
  
  <div bind:this={axis} class="axis">
    <div
        bind:this={icon}
        class="icon"
        style="left: {iconX}px"
        on:mousedown={handleMouseDown}
    />
  </div>
  