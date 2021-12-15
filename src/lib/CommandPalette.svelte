<script lang="ts">
	import { onMount } from 'svelte'
	import CommandSelector from "./CommandSelector.svelte";

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

<div id="command-palette-container" class:backdrop={isVisible}>
	{#if isVisible}
		<CommandSelector {...$$props} onClose={() => isVisible = false} />
	{/if}
</div>

<style>
	#command-palette-container {
		position: absolute;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: none;
		transition: background-color 100ms;
		padding: 1rem;
		padding-top: 5rem;
	}
		#command-palette-container.backdrop {
			background-color: rgba(0, 0, 0, 0.2);
		}
</style>