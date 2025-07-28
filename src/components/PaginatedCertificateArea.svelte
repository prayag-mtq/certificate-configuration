<script lang="ts">
	import { certificate } from '../lib/store';
	import { onMount, tick } from 'svelte';

	// Import all possible section components
	import HeaderSection from './certificate/HeaderSection.svelte';
	import CustomerDetailsSection from './certificate/CustomerDetailsSection.svelte';
	import CalibrationDataSection from './certificate/CalibrationDataSection.svelte';
	import ReferenceInstrumentSection from './certificate/ReferenceInstrumentSection.svelte';
	import TraceabilityStatementSection from './certificate/TraceabilityStatementSection.svelte';
	import UncertaintyStatementSection from './certificate/UncertaintyStatementSection.svelte';
	import FooterSection from './certificate/FooterSection.svelte';
	import CustomFieldSection from './certificate/CustomFieldSection.svelte';

	const componentMap: Record<string, any> = {
		HeaderSection,
		CustomerDetailsSection,
		CalibrationDataSection,
		ReferenceInstrumentSection,
		TraceabilityStatementSection,
		UncertaintyStatementSection,
		FooterSection,
		CustomFieldSection
	};

	let sectionElements: HTMLElement[] = [];
	let containerElement: HTMLElement;

	// Convert mm to pixels (assuming 96 DPI)
	const mmToPx = (mm: number) => (mm * 96) / 25.4;

	$: pageHeight = mmToPx($certificate.page.height);
	$: pageWidth = mmToPx($certificate.page.width);
	$: marginTop = mmToPx($certificate.page.margin.top);
	$: marginBottom = mmToPx($certificate.page.margin.bottom);
	$: marginLeft = mmToPx($certificate.page.margin.left);
	$: marginRight = mmToPx($certificate.page.margin.right);
	$: contentHeight = pageHeight - marginTop - marginBottom;

	// Reactive declaration for page styles
	$: pageStyles = `
		width: ${pageWidth}px;
		height: ${pageHeight}px;
		padding: ${marginTop}px ${marginRight}px ${marginBottom}px ${marginLeft}px;
	`;

	let paginatedSections: Array<{ pageIndex: number; sections: typeof $certificate.sections }> = [];

	function calculatePagination() {
		if (!containerElement || sectionElements.length === 0) return;

		paginatedSections = [];
		let currentPage = 0;
		let currentPageHeight = 0;
		let currentPageSections: typeof $certificate.sections = [];

		sectionElements.forEach((element, index) => {
			if (!element) return;

			const section = $certificate.sections[index];
			const sectionHeight = element.offsetHeight;

			// Check if this section would overflow the current page
			if (currentPageHeight + sectionHeight > contentHeight && currentPageSections.length > 0) {
				// Save current page and start a new one
				paginatedSections.push({
					pageIndex: currentPage,
					sections: [...currentPageSections]
				});

				currentPage++;
				currentPageHeight = 0;
				currentPageSections = [];
			}

			// Add section to current page
			currentPageSections.push(section);
			currentPageHeight += sectionHeight;
		});

		// Add the last page if it has sections
		if (currentPageSections.length > 0) {
			paginatedSections.push({
				pageIndex: currentPage,
				sections: [...currentPageSections]
			});
		}

		// Update store with pagination info
		certificate.update((state) => ({
			...state,
			pagination: {
				...state.pagination,
				totalPages: paginatedSections.length,
				pageBreaks: paginatedSections
					.slice(1)
					.map((page) => page.sections[0]?.id)
					.filter(Boolean)
			}
		}));
	}

	// Recalculate pagination when sections or page dimensions change
	$: if ($certificate.pagination.enabled && $certificate.sections) {
		tick().then(() => {
			setTimeout(calculatePagination, 100);
		});
	}

	onMount(() => {
		if ($certificate.pagination.enabled) {
			setTimeout(calculatePagination, 100);
		}
	});
</script>

<div class="flex flex-col items-center gap-8" bind:this={containerElement}>
	{#if $certificate.pagination.enabled && paginatedSections.length > 0}
		<!-- Paginated view -->
		{#each paginatedSections as page, pageIndex}
			<div class="certificate-page" style={pageStyles} data-page={pageIndex + 1}>
				<div class="h-full overflow-hidden relative">
					{#each page.sections as section (section.id)}
						<div bind:this={sectionElements[section.id - 1]}>
							{#if section.isCustom && section.customData?.fieldId}
								<svelte:component
									this={componentMap[section.component]}
									fieldId={section.customData.fieldId}
								/>
							{:else}
								<svelte:component this={componentMap[section.component]} />
							{/if}
						</div>
					{/each}
				</div>

				<!-- Page number footer -->
				<div class="page-number">
					Page {pageIndex + 1} of {paginatedSections.length}
				</div>
			</div>
		{/each}
	{:else}
		<!-- Single page view for measurement -->
		<div class="certificate-page measurement-page" style={pageStyles}>
			<div class="h-full overflow-hidden relative">
				{#each $certificate.sections as section, index (section.id)}
					<div bind:this={sectionElements[index]}>
						{#if section.isCustom && section.customData?.fieldId}
							<svelte:component
								this={componentMap[section.component]}
								fieldId={section.customData.fieldId}
							/>
						{:else}
							<svelte:component this={componentMap[section.component]} />
						{/if}
					</div>
				{/each}
			</div>
		</div>
	{/if}
</div>

<style>
	.certificate-page {
		background: white;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
		border: 1px solid #e5e7eb;
		position: relative;
		margin-bottom: 2rem;
	}

	.measurement-page {
		opacity: 0;
		position: absolute;
		top: -9999px;
		left: -9999px;
		pointer-events: none;
	}

	.page-number {
		position: absolute;
		bottom: 8px;
		right: 12px;
		font-size: 0.75rem;
		color: #6b7280;
		background: rgba(255, 255, 255, 0.9);
		padding: 4px 8px;
		border-radius: 4px;
		border: 1px solid #e5e7eb;
	}

	@media print {
		.certificate-page {
			box-shadow: none;
			border: none;
			margin-bottom: 0;
			page-break-after: always;
		}

		.certificate-page:last-child {
			page-break-after: auto;
		}

		.page-number {
			display: none;
		}
	}
</style>
