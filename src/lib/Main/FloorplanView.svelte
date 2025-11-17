<script lang="ts">
	import PictureElements from '$lib/Main/PictureElements.svelte';
	import { editMode, lang } from '$lib/Stores';
	import type { Views } from '$lib/Types';

	export let view: Views;

	$: floorplan = view?.floorplan;
	$: isEmpty = !floorplan?.elements?.length;
</script>

<main class="floorplan">
	{#if floorplan}
		<div class="canvas" class:empty={isEmpty}>
			<PictureElements sel={floorplan} variant="view" height="100%" width="100%" />

			{#if isEmpty}
				<div class="overlay" aria-live="polite">
					{$lang($editMode ? 'floorplan_empty_edit' : 'floorplan_empty')}
				</div>
			{/if}
		</div>
	{:else}
		<div class="empty-state">
			{$lang('floorplan_missing')}
		</div>
	{/if}
</main>

<style>
	main {
		grid-area: main;
		padding: 0 2rem 2rem;
		display: flex;
		align-items: stretch;
		min-height: 0;
	}

	.canvas {
		position: relative;
		flex: 1;
		min-height: min(60vh, 32rem);
		border-radius: 0.6rem;
		overflow: hidden;
	}

	.canvas.empty {
		background-color: var(--theme-button-background-color-off);
	}

	.overlay {
		position: absolute;
		inset: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		text-align: center;
		padding: 1rem;
		font-weight: 500;
		color: rgba(255, 255, 255, 0.8);
		font-size: 1.05rem;
		pointer-events: none;
	}

	.empty-state {
		flex: 1;
		display: grid;
		place-items: center;
		font-weight: 500;
		color: rgba(255, 255, 255, 0.8);
		border-radius: 0.6rem;
		background-color: var(--theme-button-background-color-off);
		min-height: min(60vh, 32rem);
		text-align: center;
		padding: 1rem;
	}

	@media all and (max-width: 768px) {
		main {
			padding: 0 1.25rem 1.25rem;
		}
	}
</style>
