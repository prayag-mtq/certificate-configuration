<!-- 
  src/lib/components/SectionPanel.svelte
  Displays a draggable list of certificate sections.
-->
<script lang="ts">
	import { certificate } from '../lib/store';
	import { dndzone } from 'svelte-dnd-action';

	const flipDurationMs = 300;

	// These handlers update the store when the user drags and drops a section.
	function handleDndConsider(e: CustomEvent) {
		$certificate.sections = e.detail.items;
	}

	function handleDndFinalize(e: CustomEvent) {
		$certificate.sections = e.detail.items;
	}
</script>

<div class="panel-container">
	<h2 class="panel-title">Sections</h2>
	<p class="panel-description">Drag to reorder the certificate sections.</p>

	<!-- The dndzone action enables drag-and-drop on this list -->
	<ul
		class="section-list"
		use:dndzone={{ items: $certificate.sections, flipDurationMs }}
		on:consider={handleDndConsider}
		on:finalize={handleDndFinalize}
	>
		<!-- Loop through the sections from the store -->
		{#each $certificate.sections as section (section.id)}
			<li class="draggable-item">
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
					class="drag-handle"
					><circle cx="12" cy="5" r="1"></circle><circle cx="12" cy="12" r="1"></circle><circle
						cx="12"
						cy="19"
						r="1"
					></circle></svg
				>
				{section.name}
			</li>
		{/each}
	</ul>
</div>

<style>
	.panel-container {
		padding: 1.5rem;
	}
	.panel-title {
		font-size: 1.25rem;
		font-weight: 600;
		margin-bottom: 0.5rem;
	}
	.panel-description {
		font-size: 0.875rem;
		color: #666;
		margin-bottom: 1.5rem;
	}
	.section-list {
		list-style: none;
		padding: 0;
	}
	.draggable-item {
		padding: 0.75rem 1rem;
		background-color: #f9f9f9;
		border: 1px solid #e0e0e0;
		border-radius: 6px;
		margin-bottom: 0.5rem;
		cursor: grab;
		display: flex;
		align-items: center;
		gap: 0.75rem;
		font-weight: 500;
		transition:
			background-color 0.2s ease,
			box-shadow 0.2s ease;
	}
	.draggable-item:hover {
		background-color: #f0f0f0;
	}
	/* This class is applied by svelte-dnd-action while dragging */
	.draggable-item:global(.svelte-dnd-action-dragging) {
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		background-color: #fff;
	}
	.drag-handle {
		color: #999;
	}
</style>
