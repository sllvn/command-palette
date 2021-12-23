<script lang="ts">
	import { onMount } from 'svelte'
	import Fuse from 'fuse.js'

	import type { Command } from './types'
	import CommandItem from './CommandItem.svelte'

	let commands: Command[]
	try {
		// @ts-ignore
		commands = window.__getCommands__()
	} catch (err) {
		console.warn('No command palette commands found.')
		commands = []
	}
	export let onClose: Function

	let query = ''
	let queryInput
	let selectedIndex = 0
	// TODO: contexts for commands, a la chrome

	const options = {
		keys: ['name'],
		threshold: 0.2
	}

	let shortcuts = {}

	const fuse = new Fuse(commands, options)

	// @ts-ignore
	$: results = query ? fuse.search(query) : fuse._docs.map((item) => ({item, matches: [], score: 1})) as Fuse.FuseResult<Command>[]
	$: {
		query; // hack to reset selectedIndex when query updates
		selectedIndex = 0;
	}

	onMount(() => {
		queryInput.focus()
		window.addEventListener('keydown', handleKeydown)
		// TODO: register shortcuts

		return () => window.removeEventListener('keydown', handleKeydown)
	})

	function handleSelect (e: CustomEvent) {
		const { command }: { command: Command } = e.detail
		const event = new CustomEvent(command.action)
		window.dispatchEvent(event)
		onClose()
	}

	function handleFocus (e: CustomEvent) {
		const { index } = e.detail
		selectedIndex = index
	}

	function handleKeydown (e: KeyboardEvent) {
		const action = {
			Tab: () => {
				e.preventDefault()
				queryInput && queryInput.focus()
			},
			ArrowDown: () => selectedIndex = (selectedIndex + 1) % results.length,
			ArrowUp: () => selectedIndex = selectedIndex === 0 ? results.length - 1 : selectedIndex - 1,
			Enter: () => handleSelect(new CustomEvent('keyboard-selected', { detail: { command: results[selectedIndex].item } })),
		}[e.key] || (() => {})
		action()
	}
</script>

<div id="command-palette-selector">
	<input
		id="query"
		type="text"
		bind:value={query}
		bind:this={queryInput}
		autocomplete='off'
	/>

	{#if results.length > 0}
		<ul id="results">
			{#each results as result, index (result.item.name)}
				<CommandItem {result} {index} {selectedIndex} on:focus={handleFocus} on:select={handleSelect} />
			{/each}
		</ul>
	{:else}
		<div id="no-results">
			No commands found.
		</div>
	{/if}
</div>

<style>
	#command-palette-selector {
		background-color: #fff;
		margin: 0 auto;
		min-width: 400px;
		max-width: 700px;
		border: 1px solid #ccc;
		border-radius: 4px;
		padding: 0.5rem;
		font-size: 16px;
		box-shadow: 3px 3px 3px #ccc;
	}
	#query {
		width: 100%;
		padding: 1rem;
		border: 1px solid #ccc;
		border-radius: 4px;
		font-size: 1rem;
	}

	#results {
		list-style: none;
		padding: 0;
		margin: 0;
		margin-top: 0.5rem;
		max-height: 400px;
		overflow-x: hidden;
		overflow-y: auto;
	}

	#no-results {
		font-style: italic;
		text-align: center;
		padding: 1rem;
	}
</style>