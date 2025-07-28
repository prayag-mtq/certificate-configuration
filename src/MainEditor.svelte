<script>
	import './app.css'
	import SectionPanel from './components/SectionPanel.svelte'; // The left panel for dragging/reordering sections.
	import PaginatedCertificateArea from './components/PaginatedCertificateArea.svelte'; // The central canvas for the live preview.
	import SettingsPanel from './components/SettingsPanel.svelte'; // The right panel for editing properties.
</script>

<main class="h-screen w-screen bg-gray-100 p-4 grid grid-cols-[320px_1fr_360px] gap-4 overflow-hidden">
	<!-- Left Panel -->
	<div class="bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden">
		<SectionPanel />
	</div>

	<!-- Center Canvas Area -->
	<div class="bg-gray-50 rounded-lg border border-gray-200 flex justify-center items-start p-8 overflow-auto">
		<PaginatedCertificateArea />
	</div>

	<!-- Right Panel -->
	<div class="bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden">
		<SettingsPanel />
	</div>
</main>


