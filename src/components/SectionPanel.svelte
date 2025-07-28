<script lang="ts">
	import { certificate } from '../lib/store';
	import { dndzone } from 'svelte-dnd-action';
	import CustomFieldEditor from './CustomFieldEditor.svelte';

	const flipDurationMs = 300;

	let showCustomFieldEditor = false;
	let editingFieldId: string | null = null;
	let isNewField = false;

	// These handlers update the store when the user drags and drops a section.
	function handleDndConsider(e: CustomEvent) {
		$certificate.sections = e.detail.items;
	}

	function handleDndFinalize(e: CustomEvent) {
		$certificate.sections = e.detail.items;
	}

	function createCustomField() {
		isNewField = true;
		editingFieldId = null;
		showCustomFieldEditor = true;
	}

	function editCustomField(section: any) {
		if (section.isCustom && section.customData?.fieldId) {
			isNewField = false;
			editingFieldId = section.customData.fieldId;
			showCustomFieldEditor = true;
		}
	}

	function deleteCustomField(section: any) {
		if (section.isCustom && section.customData?.fieldId) {
			if (confirm(`Are you sure you want to delete "${section.name}"?`)) {
				certificate.update(state => {
					// Remove from custom fields
					const newCustomFields = { ...state.customFields };
					delete newCustomFields[section.customData.fieldId];

					// Remove from sections
					const newSections = state.sections.filter(s => s.id !== section.id);

					return {
						...state,
						customFields: newCustomFields,
						sections: newSections
					};
				});
			}
		}
	}

	function closeEditor() {
		showCustomFieldEditor = false;
		editingFieldId = null;
		isNewField = false;
	}
</script>

<div class="p-6 h-full flex flex-col">
	<h2 class="text-xl font-bold text-gray-900 mb-2">Sections</h2>
	<p class="text-sm text-gray-600 mb-6">Drag to reorder the certificate sections.</p>

	<div class="mb-6">
		<button 
			class="w-full px-4 py-3 bg-black hover:bg-gray-800 text-white font-medium rounded-lg transition-colors"
			on:click={createCustomField}
		>
			+ Add Custom Field
		</button>
	</div>

	<ul
		class="flex-1 space-y-3 overflow-y-auto"
		use:dndzone={{ items: $certificate.sections, flipDurationMs }}
		on:consider={handleDndConsider}
		on:finalize={handleDndFinalize}
	>
		{#each $certificate.sections as section (section.id)}
			<li class="bg-white border border-gray-200 rounded-lg p-4 cursor-grab hover:bg-gray-50 hover:border-gray-300 transition-all flex items-center gap-3 group">
				<!-- Drag handle icon -->
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="16"
					height="16"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="text-gray-400 group-hover:text-gray-600 flex-shrink-0"
				>
					<circle cx="12" cy="5" r="1"></circle>
					<circle cx="12" cy="12" r="1"></circle>
					<circle cx="12" cy="19" r="1"></circle>
				</svg>
				
				<span class="flex-1 text-gray-900 font-medium">{section.name}</span>
				
				{#if section.isCustom}
					<div class="flex gap-2 opacity-0 group-hover:opacity-100 transition-opacity">
						<button 
							class="w-8 h-8 bg-gray-100 hover:bg-gray-200 rounded-md flex items-center justify-center text-sm transition-colors" 
							on:click|stopPropagation={() => editCustomField(section)}
							title="Edit custom field"
						>
							✏️
						</button>
						<button 
							class="w-8 h-8 bg-gray-100 hover:bg-red-100 hover:text-red-600 rounded-md flex items-center justify-center text-sm transition-colors" 
							on:click|stopPropagation={() => deleteCustomField(section)}
							title="Delete custom field"
						>
							🗑️
						</button>
					</div>
				{:else}
					<span class="text-xs bg-gray-100 text-gray-600 px-2 py-1 rounded-full font-medium">Built-in</span>
				{/if}
			</li>
		{/each}
	</ul>

	<CustomFieldEditor 
		bind:isOpen={showCustomFieldEditor}
		bind:fieldId={editingFieldId}
		bind:isNewField={isNewField}
		on:close={closeEditor}
	/>
</div>


