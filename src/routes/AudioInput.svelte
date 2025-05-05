<script lang="ts">
    import { onMount } from 'svelte';

    let {
        src = $bindable("")
    } = $props();
  
    // Handle file input and store video in IndexedDB
    async function handleFile(event: Event) {
        const input = event.target as HTMLInputElement;
        const file = input.files?.[0];
        if (!file) return;
        const url = URL.createObjectURL(file);
        src = url;
    }
</script>

<input type="file" accept="audio/mp3" onchange={handleFile} />  