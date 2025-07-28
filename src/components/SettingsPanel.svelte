<!-- 
  src/lib/components/SettingsPanel.svelte
  Provides controls to edit certificate properties, bound to the store.
-->
<script lang="ts">
	import { certificate } from '../lib/store';
	import jsPDF from 'jspdf';
	import html2canvas from 'html2canvas';

	async function triggerExport() {
		try {
			const certificatePages = document.querySelectorAll(
				'.certificate-page:not(.measurement-page)'
			);
			if (certificatePages.length === 0) {
				alert('Certificate preview not found');
				return;
			}

			// Convert dimensions from mm to points (1 mm = 2.834645669 points)
			const mmToPoints = 2.834645669;
			const pdfWidth = $certificate.page.width * mmToPoints;
			const pdfHeight = $certificate.page.height * mmToPoints;

			const pdf = new jsPDF({
				orientation: pdfWidth > pdfHeight ? 'landscape' : 'portrait',
				unit: 'pt',
				format: [pdfWidth, pdfHeight]
			});

			// Process each page
			for (let i = 0; i < certificatePages.length; i++) {
				const pageElement = certificatePages[i] as HTMLElement;
				const canvas = await html2canvas(pageElement, {
					scale: 2, // Higher quality
					useCORS: true,
					allowTaint: true
				});
				const imgData = canvas.toDataURL('image/png');

				if (i > 0) {
					pdf.addPage();
				}
				pdf.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight);
			}

			pdf.save(`certificate-${$certificate.data.certificateNo}.pdf`);
		} catch (error) {
			console.error('Error generating PDF:', error);
			alert('Error generating PDF. Please try again.');
		}
	}

	function togglePagination() {
		certificate.update((state) => ({
			...state,
			pagination: {
				...state.pagination,
				enabled: !state.pagination.enabled
			}
		}));
	}
</script>

<div class="panel-container">
	<!-- Action Buttons Header -->
	<div class="actions-header">
		<button class="btn btn-secondary">Edit</button>
		<button class="btn btn-primary" on:click={triggerExport}>Download/Export</button>
	</div>

	<!-- Page Setup Group -->
	<div class="settings-group">
		<h3 class="group-title">Page Setup</h3>
		<div class="form-item">
			<label for="format">Format</label>
			<select id="format" bind:value={$certificate.page.format}>
				<option>A4</option>
				<option>Letter</option>
				<option>Custom</option>
			</select>
		</div>
		<div class="form-item">
			<label for="unit">Unit</label>
			<select id="unit" bind:value={$certificate.page.unit}>
				<option>mm</option>
				<option>px</option>
				<option>in</option>
			</select>
		</div>
		<div class="grid-2">
			<div class="form-item">
				<label for="width">Width</label>
				<input id="width" type="number" bind:value={$certificate.page.width} />
			</div>
			<div class="form-item">
				<label for="height">Height</label>
				<input id="height" type="number" bind:value={$certificate.page.height} />
			</div>
		</div>
	</div>

	<!-- Margin Group -->
	<div class="settings-group">
		<h3 class="group-title">Margin ({$certificate.page.unit})</h3>
		<div class="grid-2">
			<div class="form-item">
				<label for="margin-top">Top</label>
				<input id="margin-top" type="number" bind:value={$certificate.page.margin.top} />
			</div>
			<div class="form-item">
				<label for="margin-right">Right</label>
				<input id="margin-right" type="number" bind:value={$certificate.page.margin.right} />
			</div>
		</div>
		<div class="grid-2">
			<div class="form-item">
				<label for="margin-bottom">Bottom</label>
				<input id="margin-bottom" type="number" bind:value={$certificate.page.margin.bottom} />
			</div>
			<div class="form-item">
				<label for="margin-left">Left</label>
				<input id="margin-left" type="number" bind:value={$certificate.page.margin.left} />
			</div>
		</div>
	</div>

	<!-- Pagination Group -->
	<div class="settings-group">
		<h3 class="group-title">Pagination</h3>
		<div class="form-item">
			<label class="checkbox-label">
				<input
					type="checkbox"
					bind:checked={$certificate.pagination.enabled}
					on:change={togglePagination}
				/>
				Enable automatic page breaks
			</label>
		</div>
		{#if $certificate.pagination.enabled}
			<div class="pagination-info">
				<p>Total Pages: <strong>{$certificate.pagination.totalPages}</strong></p>
				{#if $certificate.pagination.pageBreaks.length > 0}
					<p>Page breaks after sections:</p>
					<ul class="page-breaks-list">
						{#each $certificate.pagination.pageBreaks as breakId}
							{@const section = $certificate.sections.find((s) => s.id === breakId)}
							{#if section}
								<li>{section.name}</li>
							{/if}
						{/each}
					</ul>
				{/if}
			</div>
		{/if}
	</div>

	<!-- Metadata Group -->
	<div class="settings-group">
		<h3 class="group-title">Metadata</h3>
		<div class="form-item">
			<label for="work">Work</label>
			<input id="work" type="text" bind:value={$certificate.metadata.work} />
		</div>
		<div class="form-item">
			<label for="version">Version #</label>
			<input id="version" type="text" bind:value={$certificate.metadata.version} />
		</div>
	</div>
</div>

<style>
	.panel-container {
		padding: 1.5rem;
		display: flex;
		flex-direction: column;
		gap: 1.5rem;
	}
	.actions-header {
		display: flex;
		gap: 0.5rem;
	}
	.btn {
		flex-grow: 1;
		padding: 0.65rem 1rem;
		border: none;
		border-radius: 6px;
		font-weight: 600;
		cursor: pointer;
		transition: all 0.2s ease;
	}
	.btn-primary {
		background-color: #007bff;
		color: white;
	}
	.btn-primary:hover {
		background-color: #0056b3;
	}
	.btn-secondary {
		background-color: #f0f0f0;
		color: #333;
		border: 1px solid #ccc;
	}
	.btn-secondary:hover {
		background-color: #e0e0e0;
	}
	.settings-group {
		border-top: 1px solid #eee;
		padding-top: 1.5rem;
	}
	.group-title {
		font-size: 1rem;
		font-weight: 600;
		margin-bottom: 1rem;
		color: #333;
	}
	.form-item {
		margin-bottom: 1rem;
	}
	.form-item label {
		display: block;
		font-size: 0.875rem;
		font-weight: 500;
		margin-bottom: 0.35rem;
		color: #555;
	}
	.form-item input,
	.form-item select {
		width: 100%;
		padding: 0.5rem;
		border: 1px solid #ccc;
		border-radius: 4px;
		box-sizing: border-box;
	}
	.grid-2 {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 1rem;
	}
	.checkbox-label {
		display: flex !important;
		align-items: center;
		gap: 0.5rem;
		cursor: pointer;
		font-weight: normal !important;
	}
	.checkbox-label input[type="checkbox"] {
		width: auto !important;
		margin: 0;
	}
	.pagination-info {
		background-color: #f9f9f9;
		padding: 0.75rem;
		border-radius: 4px;
		font-size: 0.85rem;
	}
	.pagination-info p {
		margin: 0.25rem 0;
	}
	.page-breaks-list {
		margin: 0.5rem 0 0 1rem;
		padding: 0;
		font-size: 0.8rem;
	}
	.page-breaks-list li {
		margin-bottom: 0.25rem;
	}
</style>
