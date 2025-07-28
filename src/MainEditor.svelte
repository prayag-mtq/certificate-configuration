<script>
	import SectionPanel from './components/SectionPanel.svelte'; // The left panel for dragging/reordering sections.
	import CertificateArea from './components/CertificateArea.svelte'; // The central canvas for the live preview.
	import SettingsPanel from './components/SettingsPanel.svelte'; // The right panel for editing properties.
</script>

<main class="editor-container">
	<!-- Left Panel Slot -->
	<div class="editor-panel-left">
		<SectionPanel />
	</div>

	<!-- Center Canvas Area -->
	<div class="editor-canvas-area">
		<CertificateArea />
	</div>

	<!-- Right Panel Slot -->
	<div class="editor-panel-right">
		<SettingsPanel />
	</div>
</main>

<style>
	/* Main container for the editor, using CSS Grid for the layout.
	  This creates a three-column structure that fills the full viewport height.
	*/
	.editor-container {
		display: grid;
		/* Defines the three columns: a fixed left panel, a flexible center area, and a fixed right panel. */
		grid-template-columns: 280px 1fr 320px;
		height: 100vh; /* Full viewport height */
		width: 100vw; /* Full viewport width */
		gap: 1rem; /* A small gap between the columns */
		background-color: #f0f2f5; /* A neutral background for the entire editor */
		padding: 1rem;
		box-sizing: border-box;
	}

	/* Styling for the left panel container */
	.editor-panel-left {
		background-color: #ffffff;
		border-radius: 8px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
		overflow-y: auto; /* Allows scrolling if content overflows */
	}

	/* Styling for the central canvas area */
	.editor-canvas-area {
		display: flex;
		justify-content: center; /* Center the certificate horizontally */
		align-items: flex-start; /* Align to the top */
		padding: 2rem;
		overflow: auto; /* Allows scrolling for the canvas itself */
		background-color: #e9ebee;
		border-radius: 8px;
	}

	/* Styling for the right panel container */
	.editor-panel-right {
		background-color: #ffffff;
		border-radius: 8px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
		overflow-y: auto; /* Allows scrolling if content overflows */
	}

	/* Basic scrollbar styling for a cleaner look */
	::-webkit-scrollbar {
		width: 6px;
	}
	::-webkit-scrollbar-track {
		background: #f1f1f1;
	}
	::-webkit-scrollbar-thumb {
		background: #ccc;
		border-radius: 6px;
	}
	::-webkit-scrollbar-thumb:hover {
		background: #aaa;
	}
</style>
