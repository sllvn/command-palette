<script lang="ts">
	import { onMount } from 'svelte'
	import Fuse from 'fuse.js'

	let query = ''
	let queryInput

	let selectedIndex = 0
	// TODO: contexts for commands, a la chrome

	const options = {
		keys: ['name'],
		threshold: 0.3
	}

	interface Command {
		name: string
		shortcut?: string
	}
	let commands: Command[] = [
		{ name: 'Preferences', shortcut: 'cmd-p' },
		{ name: 'Open File', shortcut: 'cmd-o' },
		{ name: 'New Project', shortcut: 'cmd-n' },
	]

	const fuse = new Fuse(commands, options)

	// @ts-ignore
	$: results = query ? fuse.search(query) : fuse._docs.map((item) => ({item, matches: [], score: 1}))
	$: {
		query; // hack to reset selectedIndex when query updates
		selectedIndex = 0;
	}

	onMount(() => {
		queryInput.focus()
		window.addEventListener('keydown', handleKeydown)
	})

	function handleSelect (command: Command) {
		console.log('handleSelect', command)
	}

	function handleMouseover (index: number) {
		selectedIndex = index
	}

	function handleKeydown (e: KeyboardEvent) {
		if (e.key === 'Tab') {
			e.preventDefault()
			queryInput.focus()
		} else if (e.key === 'ArrowDown') {
			selectedIndex = (selectedIndex + 1) % results.length
		} else if (e.key === 'ArrowUp') {
			selectedIndex = selectedIndex === 0 ? results.length - 1 : selectedIndex - 1
		}
	}
</script>

<div id="command-palette">
	<input
		id="query"
		type="text"
		bind:value={query}
		bind:this={queryInput}
		autocomplete='off'
	/>

	{#if results.length > 0}
	<ul id="results">
		{#each results as result, index}
			<li class:selected={index === selectedIndex}>
				<button
					on:click={() => handleSelect(result.item)}
					on:mouseover={() => handleMouseover(index)}
					on:focus={() => handleMouseover(index)}
				>
					<div class="name">
						{result.item.name}
					</div>
					<div class="shortcut">
						{result.item.shortcut.replace('cmd-', '⌘').toUpperCase()}
					</div>
				</button>
			</li>
		{/each}
	</ul>
	{:else}
	<div id="no-results">
		No commands found.
	</div>
	{/if}
</div>

<style>
	#command-palette {
		min-width: 300px;
		max-width: 700px;
		border: 1px solid #ccc;
		border-radius: 4px;
		padding: 0.5rem;
		font-size: 16px;
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
	}
	#results li {
		padding: 0;
		border-bottom: 1px solid #ccc;
	}
		#results li:last-child { border-bottom: 0; }
		#results li button {
			font-size: 1rem;
			display: block;
			width: 100%;
			text-align: left;
			padding: 1rem;
			text-decoration: none;
			color: inherit;
			background: none;
			border: 2px solid rgba(255, 255, 255, 0);
			border-radius: 4px;
			cursor: pointer;
			display: flex;
			justify-content: space-between;
		}
			#results li.selected button {
				background-color: #ebf0ff;
				border: 2px solid #a7bdff;
			}
			#results li button .shortcut {
				font-size: 0.875rem;
				opacity: 0.5;
			}

	#no-results {
		font-style: italic;
		text-align: center;
		padding: 1rem;
	}
</style>