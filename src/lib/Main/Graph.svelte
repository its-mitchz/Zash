<script lang="ts">
	import Graph from '$lib/Sidebar/Graph.svelte';
	import { editMode, itemHeight } from '$lib/Stores';
	import { openModal } from 'svelte-modals';
	import type { GraphItem } from '$lib/Types';

	export let sel: GraphItem;
	export let demo: string | undefined = undefined;
	export let preview = false;
	export let height = '8rem';

	const minRows = 3;

	function handleActivate(event?: KeyboardEvent) {
		if (preview || !$editMode) return;

		if (event) {
			const keys = ['Enter', ' '];
			if (!keys.includes(event.key)) return;
			event.preventDefault();
		}

		openModal(() => import('$lib/Modal/GraphConfig.svelte'), { sel });
	}
</script>

{#if preview}
	<div class="graph-card preview" style:min-height={`calc(${$itemHeight}px * ${minRows})`}>
		<Graph
			entity_id={sel?.entity_id || demo}
			name={sel?.name}
			period={sel?.period}
			stroke={sel?.stroke ?? 2}
			timelineHeight={height}
		/>
	</div>
{:else}
	<button
		class="graph-card"
		type="button"
		tabindex={$editMode ? 0 : -1}
		aria-disabled={!$editMode}
		on:click={() => handleActivate()}
		on:keydown={handleActivate}
		style:cursor={$editMode ? 'pointer' : 'default'}
		style:min-height={`calc(${$itemHeight}px * ${minRows})`}
	>
		<Graph
			entity_id={sel?.entity_id || demo}
			name={sel?.name}
			period={sel?.period ?? 'day'}
			stroke={sel?.stroke ?? 2}
			timelineHeight={height}
		/>
	</button>
{/if}

<style>
	.graph-card {
		display: block;
		width: 100%;
		height: 100%;
		background: var(--theme-button-background-color-off, rgba(0, 0, 0, 0.2));
		border-radius: 0.65rem;
		color: var(--theme-button-name-color-off, white);
		box-sizing: border-box;
		--theme-sidebar-item-padding: 1rem;
	}

	.graph-card:focus-visible {
		outline: 2px solid rgba(255, 255, 255, 0.6);
		outline-offset: 4px;
	}

	:global(.graph-card .container) {
		width: 100%;
	}

	.preview {
		cursor: default;
	}
</style>
