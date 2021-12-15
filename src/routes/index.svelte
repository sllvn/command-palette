<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import CommandPaletteContainer from '$lib/CommandPaletteContainer.svelte';
	import type { Command } from '$lib/types'

	const commands: Command[] = [
		{ name: 'Preferences', shortcut: 'cmd-p', action: 'myApp.openPrefs' },
		{ name: 'Open File', shortcut: 'cmd-o', action: 'myApp.openFile' },
		{ name: 'New Project', shortcut: 'cmd-n', action: 'myApp.newProject' },
		{ name: 'View Recent Projects', action: 'myApp.recentProjects' },
		{ name: 'Delete Project', shortcut: 'cmd-d', action: 'myApp.deleteProject' },
		{ name: 'Duplicate Project', action: 'myApp.duplicateProject' },
		{ name: 'Share', action: 'myApp.share' },
		{ name: 'Some Action 1', action: 'myApp.someAction1' },
		{ name: 'Some Action 2', action: 'myApp.someAction2' },
		{ name: 'Some Action 3', action: 'myApp.someAction3' },
	]
</script>

<CommandPaletteContainer {commands} />

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

