<!-- 
  src/lib/components/CertificateArea.svelte
  Renders the live preview of the certificate based on the store.
-->
<script lang="ts">
	import { certificate } from '../lib/store';

	// Import all possible section components that can be rendered.
	// In a real app, you might lazy-load these for performance.
	import HeaderSection from './certificate/HeaderSection.svelte';
	import CustomerDetailsSection from './certificate/CustomerDetailsSection.svelte';
	import CalibrationDataSection from './certificate/CalibrationDataSection.svelte';
	import ReferenceInstrumentSection from './certificate/ReferenceInstrumentSection.svelte';
	import TraceabilityStatementSection from './certificate/TraceabilityStatementSection.svelte';
	import UncertaintyStatementSection from './certificate/UncertaintyStatementSection.svelte';
	import FooterSection from './certificate/FooterSection.svelte';

	// A map to look up the component constructor from the string name in the store.
	const componentMap: Record<string, any> = {
		HeaderSection,
		CustomerDetailsSection,
		CalibrationDataSection,
		ReferenceInstrumentSection,
		TraceabilityStatementSection,
		UncertaintyStatementSection,
		FooterSection
	};

	// Reactive declaration: this string will automatically update whenever
	// any of its dependencies in the store change.
	$: pageStyles = `
      width: ${$certificate.page.width}${$certificate.page.unit};
      height: ${$certificate.page.height}${$certificate.page.unit};
      padding: ${$certificate.page.margin.top}px ${$certificate.page.margin.right}px ${$certificate.page.margin.bottom}px ${$certificate.page.margin.left}px;
    `;25
</script>

<!-- This is the element that html2canvas will capture for the PDF -->
<div class="certificate-paper" style={pageStyles}>
	<!-- Loop through the sections array from the store -->
	{#each $certificate.sections as section (section.id)}
		<!-- Use svelte:component to dynamically render the correct component -->
		<svelte:component this={componentMap[section.component]} />
	{/each}
</div>

<style>
	.certificate-paper {
		background-color: white;
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
		box-sizing: border-box;
		/* This ensures the page doesn't shrink below its specified dimensions */
		flex-shrink: 0;
		transition: all 0.3s ease;
	}
</style>
