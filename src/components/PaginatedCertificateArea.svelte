<script lang="ts">
	import { certificate } from '../lib/store';
	import { onMount, afterUpdate } from 'svelte';
	
	// Import all possible section components
	import HeaderSection from './certificate/HeaderSection.svelte';
	import CustomerDetailsSection from './certificate/CustomerDetailsSection.svelte';
	import CalibrationDataSection from './certificate/CalibrationDataSection.svelte';
	import ReferenceInstrumentSection from './certificate/ReferenceInstrumentSection.svelte';
	import TraceabilityStatementSection from './certificate/TraceabilityStatementSection.svelte';
	import UncertaintyStatementSection from './certificate/UncertaintyStatementSection.svelte';
	import FooterSection from './certificate/FooterSection.svelte';

	const componentMap: Record<string, any> = {
		HeaderSection,
		CustomerDetailsSection,
		CalibrationDataSection,
		ReferenceInstrumentSection,
		TraceabilityStatementSection,
		UncertaintyStatementSection,
		FooterSection
	};

	let pages: HTMLElement[] = [];
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

	let paginatedSections: Array<{pageIndex: number, sections: typeof $certificate.sections}> = [];

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
		certificate.update(state => ({
			...state,
			pagination: {
				...state.pagination,
				totalPages: paginatedSections.length,
				pageBreaks: paginatedSections.slice(1).map(page => page.sections[0]?.id).filter(Boolean)
			}
		}));
	}

	// Recalculate pagination when sections or page dimensions change
	afterUpdate(() => {
		if ($certificate.pagination.enabled) {
			setTimeout(calculatePagination, 100); // Small delay to ensure DOM is updated
		}
	});

	onMount(() => {
		if ($certificate.pagination.enabled) {
			setTimeout(calculatePagination, 100);
		}
	});
</script>

<div class="certificate-container" bind:this={containerElement}>
	{#if $certificate.pagination.enabled && paginatedSections.length > 0}
		<!-- Paginated view -->
		{#each paginatedSections as page, pageIndex}
			<div class="certificate-page" style={pageStyles} data-page={pageIndex + 1}>
				<div class="page-content">
					{#each page.sections as section (section.id)}
						<div class="section-wrapper" bind:this={sectionElements[section.id - 1]}>
							<svelte:component this={componentMap[section.component]} />
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
			<div class="page-content">
				{#each $certificate.sections as section, index (section.id)}
					<div class="section-wrapper" bind:this={sectionElements[index]}>
						<svelte:component this={componentMap[section.component]} />
					</div>
				{/each}
			</div>
		</div>
	{/if}
</div>

<style>
	.certificate-container {
		display: flex;
		flex-direction: column;
		gap: 2rem;
		align-items: center;
	}

	.certificate-page {
		background-color: white;
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
		box-sizing: border-box;
		flex-shrink: 0;
		position: relative;
		margin-bottom: 1rem;
	}

	.measurement-page {
		opacity: 0;
		position: absolute;
		top: -9999px;
		left: -9999px;
		pointer-events: none;
	}

	.page-content {
		height: 100%;
		overflow: hidden;
		position: relative;
	}

	.section-wrapper {
		break-inside: avoid;
		page-break-inside: avoid;
	}

	.page-number {
		position: absolute;
		bottom: 5px;
		right: 10px;
		font-size: 0.8rem;
		color: #666;
		background: rgba(255, 255, 255, 0.8);
		padding: 2px 6px;
		border-radius: 3px;
	}

	/* Print styles */
	@media print {
		.certificate-container {
			gap: 0;
		}
		
		.certificate-page {
			box-shadow: none;
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