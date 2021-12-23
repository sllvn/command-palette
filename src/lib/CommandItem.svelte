<script lang='ts'>
	import { createEventDispatcher } from 'svelte'
	import type Fuse from 'fuse.js'

	import type { Command } from './types'

	export let index: number
	export let selectedIndex: number
	export let result: Fuse.FuseResult<Command>

	let ref: HTMLElement

	$: {
		if (ref && selectedIndex === index) {
			const bbox = ref.getBoundingClientRect()
			const container = ref.parentElement.getBoundingClientRect()

			if (bbox.top + bbox.height > container.height + container.top) {
				ref.scrollIntoView(false)
			} else if (bbox.top < container.top) {
				ref.scrollIntoView(true)
			}
		}
	}

	const dispatch = createEventDispatcher()
</script>

<li class:selected={index === selectedIndex} bind:this={ref}>
	<button
		on:click={() => dispatch('select', { command: result.item })}
		on:mousemove={() => dispatch('focus', { index })}
		on:focus={() => dispatch('focus', { index })}
	>
		<div class="name">
			{result.item.name}
		</div>
		{#if result.item.shortcut}
			<div class="shortcut">
				{result.item.shortcut.replace('cmd-', '⌘').toUpperCase()}
			</div>
		{/if}
	</button>
</li>

<style>
	li {
		padding: 0;
		border-bottom: 1px solid #ccc;
	}
		li:last-child { border-bottom: 0; }
		li button {
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
			li.selected button {
				background-color: #ebf0ff;
				border: 2px solid #a7bdff;
			}
			li button .shortcut {
				font-size: 0.875rem;
				opacity: 0.5;
			}
</style>