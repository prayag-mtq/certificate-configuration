<script lang="ts">
	import { certificate } from '../../lib/store';
</script>

<div class="traceability-statement-section">
	<h3>Traceability Statement</h3>
	<div class="statement-text">
		<p>
			This calibration was performed in accordance with requirements of ISO/IEC 17025:2017 with 
			measuring standards traceable to SI Units through NIST. Accounting for uncertainty will be 
			taken to mean that at a 95% confidence level the measurement result plus and minus the 
			expanded uncertainty (k=2) shall be totally within the specification limits. All results 
			within this certificate relate only to the item(s) calibrated. Calibration was completed 
			per above-referenced process/es/standards. Any deviation from established procedures has 
			been noted in the remarks section of this certificate.
		</p>
		<p>
			The measurement uncertainty reported has been calculated in accordance with the requirements 
			of ISO/IEC 17025:2017 and represents an expanded uncertainty expressed at approximately the 
			95% confidence level using a coverage factor of k=2.
		</p>
		<p>
			This certificate shall not be reproduced except in full, without written approval of the 
			laboratory. The results reported in this certificate relate only to the item(s) tested or 
			calibrated.
		</p>
	</div>
</div>

<style>
	.traceability-statement-section {
		margin-bottom: 1.5rem;
	}

	.traceability-statement-section h3 {
		font-size: 1.1rem;
		font-weight: bold;
		margin-bottom: 1rem;
		text-decoration: underline;
	}

	.statement-text {
		font-size: 0.9rem;
		line-height: 1.5;
		text-align: justify;
	}

	.statement-text p {
		margin-bottom: 1rem;
	}

	.statement-text p:last-child {
		margin-bottom: 0;
	}
</style>