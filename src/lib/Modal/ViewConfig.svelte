<script lang="ts">
	import { dashboard, record, lang, ripple } from '$lib/Stores';
	import { onDestroy } from 'svelte';
	import Modal from '$lib/Modal/Index.svelte';
	import Icon, { loadIcons } from '@iconify/svelte';
	import InputClear from '$lib/Components/InputClear.svelte';
	import ConfigButtons from '$lib/Modal/ConfigButtons.svelte';
	import Ripple from 'svelte-ripple';
import { updateObj, generateId } from '$lib/Utils';
import type { FloorplanConfig, ViewItem } from '$lib/Types';
	import { openModal } from 'svelte-modals';
	import { icons } from '$lib/Modal/PictureElements/icons';

	export let isOpen: boolean;
	export let sel: ViewItem;

let name = sel?.name;

let icon: string | undefined = sel?.icon;
let layout = sel?.layout || 'grid';

const nameConst = name;

function set(key: string, event?: any) {
	sel = updateObj(sel, key, event);
	$dashboard = $dashboard;
}

function ensureFloorplan(): FloorplanConfig {
	if (!sel.floorplan) {
		sel.floorplan = {
			id: sel?.id || generateId($dashboard),
			elements: []
		};
	}
	return sel.floorplan as FloorplanConfig;
}

function handleLayoutChange(value: 'grid' | 'floorplan') {
	if (layout === value) return;
	layout = value;
	set('layout', value);

	if (value === 'grid') {
		sel.sections = sel.sections || [];
	} else {
		ensureFloorplan();
	}

	$dashboard = $dashboard;
}

async function openFloorplanEditor() {
	const [PictureElementsConfig] = await Promise.all([
		import('$lib/Modal/PictureElements/PictureElementsConfig.svelte'),
		loadIcons(Object.values(icons))
	]);

	openModal(PictureElementsConfig.default, {
		sel: ensureFloorplan()
	});
}

onDestroy(() => $record());
</script>

{#if isOpen}
	<Modal>
		<h1 slot="title">{$lang('edit_view')}</h1>

		<h2>{$lang('preview')}</h2>

		<div class="preview">
			<div class="inline-preview">
				<div class="view_item">{name || nameConst}</div>
			</div>
		</div>

		<h2>{$lang('name')}</h2>

		<InputClear
			condition={name}
			on:clear={() => {
				name = '';
				set('name', nameConst);
			}}
			let:padding
		>
			<input
				class="input"
				type="text"
				bind:value={name}
				placeholder={nameConst}
				on:change={(event) => set('name', event)}
				style:padding
				autocomplete="off"
				spellcheck="false"
			/>
		</InputClear>

		<h2>{$lang('icon')} ({$lang('sidebar')?.toLocaleLowerCase()})</h2>

		<div class="icon-gallery-container">
			<InputClear
				condition={icon}
				on:clear={() => {
					icon = undefined;
					set('icon');
				}}
				let:padding
			>
				<input
					class="input"
					type="text"
					placeholder={'fluent:tab-add-24-filled'}
					bind:value={icon}
					on:change={(event) => set('icon', event)}
					style:padding
					autocomplete="off"
					spellcheck="false"
				/>
			</InputClear>

			<button
				class="icon-gallery"
				on:click={() => {
					window.open('https://icon-sets.iconify.design/', '_blank');
				}}
				title={$lang('icon')}
				use:Ripple={$ripple}
			>
				<Icon icon="vaadin:grid-small" height="none" />
			</button>
		</div>

		<h2>{$lang('layout')}</h2>

		<div class="layout-options">
			<button
				class:selected={layout !== 'floorplan'}
				type="button"
				on:click={() => handleLayoutChange('grid')}
			>
				{$lang('grid_layout')}
			</button>

			<button
				class:selected={layout === 'floorplan'}
				type="button"
				on:click={() => handleLayoutChange('floorplan')}
			>
				{$lang('floorplan')}
			</button>
		</div>

		{#if layout === 'floorplan'}
			<p class="layout-hint">
				{$lang('floorplan_help')}
			</p>

			<button class="floorplan-button" type="button" use:Ripple={$ripple} on:click={openFloorplanEditor}>
				{$lang('configure_floorplan')}
			</button>
		{/if}

		<ConfigButtons {sel} />
	</Modal>
{/if}

<style>
	.inline-preview {
		width: fit-content;
		padding: 0.6rem 0;
	}

	.view_item {
		border-bottom: 3px solid white;
		color: white;
		font-weight: 700;
		font-size: 1.2rem;
		padding-bottom: 3px;
		white-space: nowrap;
	}

	.layout-options {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(8rem, 1fr));
		gap: 0.5rem;
	}

	.layout-options button {
		border: 1px solid rgba(255, 255, 255, 0.25);
		border-radius: 0.45rem;
		padding: 0.6rem 1rem;
		background: transparent;
		color: white;
		font-weight: 500;
		cursor: pointer;
		transition: border-color 200ms ease, background-color 200ms ease;
	}

	.layout-options button.selected {
		border-color: white;
		background-color: rgba(255, 255, 255, 0.15);
	}

	.layout-options button:not(.selected):hover {
		border-color: rgba(255, 255, 255, 0.75);
	}

	.layout-hint {
		margin: 0.5rem 0 0;
		color: rgba(255, 255, 255, 0.8);
	}

	.floorplan-button {
		margin-top: 0.75rem;
		padding: 0.65rem 1rem;
		border: 1px solid rgba(255, 255, 255, 0.25);
		background: transparent;
		border-radius: 0.45rem;
		color: white;
		cursor: pointer;
		font-weight: 500;
	}
</style>
