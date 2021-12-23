<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { onMount } from 'svelte'
	import CommandPalette from '$lib/CommandPalette.svelte';
	import type { Command } from '$lib/types'

	interface FullCommand extends Command {
		handler: Function
	}
	const makeHandler = (name: string) => (e: any) => console.log('handler', name)
	const commands: FullCommand[] = [
		{ name: 'Preferences', shortcut: 'cmd-p', action: 'myApp.openPrefs', handler: makeHandler('prefs') },
		{ name: 'Open File', shortcut: 'cmd-o', action: 'myApp.openFile', handler: makeHandler('open proj') },
		{ name: 'New Project', shortcut: 'cmd-n', action: 'myApp.newProject', handler: makeHandler('new proj') },
		{ name: 'View Recent Projects', action: 'myApp.recentProjects', handler: makeHandler('view recent') },
		{ name: 'Delete Project', shortcut: 'cmd-d', action: 'myApp.deleteProject', handler: makeHandler('delete proj') },
		{ name: 'Duplicate Project', action: 'myApp.duplicateProject', handler: makeHandler('duplicate proj') },
		{ name: 'Share', action: 'myApp.share', handler: makeHandler('share') },
		{ name: 'Some Action 1', action: 'myApp.someAction1', handler: makeHandler('action 1') },
		{ name: 'Some Action 2', action: 'myApp.someAction2', handler: makeHandler('action 2') },
		{ name: 'Some Action 3', action: 'myApp.someAction3', handler: makeHandler('action 3') },
	]

	onMount(() => {
		// @ts-ignore
		window.__COMMAND_PALETTE__ = {
			name: 'Browser demo',
			specVersion: '0.1.0',
			getCommands (): Command[] {
				return commands.map(({ handler, ...rest }) => ({ ...rest }))
			}
		}

		commands.forEach(command => {
			// @ts-ignore
			window.addEventListener(command.action, command.handler)
		})

		return () => {
			commands.forEach(command => {
				// @ts-ignore
				window.removeEventListener(command.action, command.handler)
			})
		}
	})
</script>

<CommandPalette />

<div id="container">
	Press Ctrl-P to invoke the command palette.
</div>

<style>
	#container {
		font-size: 2rem;
		padding: 5rem;
		border: 1px solid green;
		background-color: lightgreen;
	}
</style>

