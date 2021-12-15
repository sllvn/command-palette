<script lang="ts">
	import { onMount } from 'svelte'
	import CommandPalette from "./CommandPalette.svelte";

	let isVisible = false

	onMount(() => {
		window.addEventListener('keydown', handleKeydown)
		return () => window.removeEventListener('keydown', handleKeydown)
	})

	function handleKeydown (e: KeyboardEvent) {
		if (e.key === 'p' && e.ctrlKey) {
			isVisible = !isVisible
		} else if (e.key === 'Escape') {
			isVisible = false
		}
	}
</script>

<div id="command-palette-container">
	{#if isVisible}
		<CommandPalette {...$$props} onClose={() => isVisible = false} />
	{/if}
</div>

<style>
	#command-palette-container {
		position: absolute;
		top: 15%;
		left: 0;
		right: 0;
		padding: 1rem;
	}
</style>