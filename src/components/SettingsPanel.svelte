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

<div class="p-6 h-full flex flex-col gap-6 overflow-y-auto">
	<!-- Action Buttons Header -->
	<div class="flex gap-3">
		<button class="flex-1 px-4 py-3 bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium rounded-lg transition-colors">
			Edit
		</button>
		<button 
			class="flex-1 px-4 py-3 bg-black hover:bg-gray-800 text-white font-medium rounded-lg transition-colors"
			onclick={triggerExport}
		>
			Download PDF
		</button>
	</div>

	<!-- Page Setup Group -->
	<div class="bg-gray-50 border border-gray-200 rounded-lg p-4">
		<h3 class="text-lg font-semibold text-gray-900 mb-4">Page Setup</h3>
		
		<div class="space-y-4">
			<div>
				<label for="format" class="block text-sm font-medium text-gray-700 mb-2">Format</label>
				<select id="format" bind:value={$certificate.page.format} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500">
					<option>A4</option>
					<option>Letter</option>
					<option>Custom</option>
				</select>
			</div>
			
			<div>
				<label for="unit" class="block text-sm font-medium text-gray-700 mb-2">Unit</label>
				<select id="unit" bind:value={$certificate.page.unit} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500">
					<option>mm</option>
					<option>px</option>
					<option>in</option>
				</select>
			</div>
			
			<div class="grid grid-cols-2 gap-4">
				<div>
					<label for="width" class="block text-sm font-medium text-gray-700 mb-2">Width</label>
					<input id="width" type="number" bind:value={$certificate.page.width} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
				<div>
					<label for="height" class="block text-sm font-medium text-gray-700 mb-2">Height</label>
					<input id="height" type="number" bind:value={$certificate.page.height} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
			</div>
		</div>
	</div>

	<!-- Margin Group -->
	<div class="bg-gray-50 border border-gray-200 rounded-lg p-4">
		<h3 class="text-lg font-semibold text-gray-900 mb-4">Margin ({$certificate.page.unit})</h3>
		
		<div class="space-y-4">
			<div class="grid grid-cols-2 gap-4">
				<div>
					<label for="margin-top" class="block text-sm font-medium text-gray-700 mb-2">Top</label>
					<input id="margin-top" type="number" bind:value={$certificate.page.margin.top} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
				<div>
					<label for="margin-right" class="block text-sm font-medium text-gray-700 mb-2">Right</label>
					<input id="margin-right" type="number" bind:value={$certificate.page.margin.right} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
			</div>
			<div class="grid grid-cols-2 gap-4">
				<div>
					<label for="margin-bottom" class="block text-sm font-medium text-gray-700 mb-2">Bottom</label>
					<input id="margin-bottom" type="number" bind:value={$certificate.page.margin.bottom} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
				<div>
					<label for="margin-left" class="block text-sm font-medium text-gray-700 mb-2">Left</label>
					<input id="margin-left" type="number" bind:value={$certificate.page.margin.left} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
				</div>
			</div>
		</div>
	</div>

	<!-- Pagination Group -->
	<div class="bg-gray-50 border border-gray-200 rounded-lg p-4">
		<h3 class="text-lg font-semibold text-gray-900 mb-4">Pagination</h3>
		
		<div class="space-y-4">
			<label class="flex items-center gap-3 cursor-pointer">
				<input 
					type="checkbox" 
					bind:checked={$certificate.pagination.enabled}
					onchange={togglePagination}
					class="w-4 h-4 text-gray-600 bg-gray-100 border-gray-300 rounded focus:ring-gray-500"
				/>
				<span class="text-sm font-medium text-gray-700">Enable automatic page breaks</span>
			</label>
			
			{#if $certificate.pagination.enabled}
				<div class="bg-white border border-gray-200 rounded-md p-3">
					<p class="text-sm font-medium text-gray-900 mb-2">Total Pages: <span class="font-bold">{$certificate.pagination.totalPages}</span></p>
					{#if $certificate.pagination.pageBreaks.length > 0}
						<p class="text-sm text-gray-700 mb-2">Page breaks after sections:</p>
						<ul class="space-y-1">
							{#each $certificate.pagination.pageBreaks as breakId}
								{@const section = $certificate.sections.find((s) => s.id === breakId)}
								{#if section}
									<li class="text-xs text-gray-600 bg-gray-100 px-2 py-1 rounded">{section.name}</li>
								{/if}
							{/each}
						</ul>
					{/if}
				</div>
			{/if}
		</div>
	</div>

	<!-- Metadata Group -->
	<div class="bg-gray-50 border border-gray-200 rounded-lg p-4">
		<h3 class="text-lg font-semibold text-gray-900 mb-4">Metadata</h3>
		
		<div class="space-y-4">
			<div>
				<label for="work" class="block text-sm font-medium text-gray-700 mb-2">Work</label>
				<input id="work" type="text" bind:value={$certificate.metadata.work} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
			</div>
			<div>
				<label for="version" class="block text-sm font-medium text-gray-700 mb-2">Version #</label>
				<input id="version" type="text" bind:value={$certificate.metadata.version} class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-gray-500" />
			</div>
		</div>
	</div>
</div>
<style>
	/* Hide scrollbar for Chrome, Safari and Opera */
	::-webkit-scrollbar {
		display: none;
	}

	/* Hide scrollbar for IE, Edge and Firefox */
	::-ms-scrollbar {
		display: none;
	}
</style>
