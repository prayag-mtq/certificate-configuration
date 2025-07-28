<script lang="ts">
	import { certificate } from '../../lib/store';
</script>

<div class="uncertainty-statement-section">
	<h3>Uncertainty Statement</h3>
	<div class="uncertainty-content">
		<p>
			The measurement uncertainties given in this certificate have been calculated in accordance 
			with the "Guide to the Expression of Uncertainty in Measurement" (GUM) and ISO/IEC 17025:2017 
			requirements. The reported expanded uncertainty is based on a standard uncertainty multiplied 
			by a coverage factor k=2, providing a level of confidence of approximately 95%.
		</p>
		<div class="uncertainty-table">
			<table>
				<thead>
					<tr>
						<th>Source of Uncertainty</th>
						<th>Type</th>
						<th>Standard Uncertainty</th>
						<th>Sensitivity Coefficient</th>
						<th>Contribution</th>
					</tr>
				</thead>
				<tbody>
					<tr>
						<td>Reference Standard</td>
						<td>B</td>
						<td>1.2%</td>
						<td>1.0</td>
						<td>1.2%</td>
					</tr>
					<tr>
						<td>Repeatability</td>
						<td>A</td>
						<td>0.8%</td>
						<td>1.0</td>
						<td>0.8%</td>
					</tr>
					<tr>
						<td>Environmental Conditions</td>
						<td>B</td>
						<td>0.5%</td>
						<td>1.0</td>
						<td>0.5%</td>
					</tr>
				</tbody>
			</table>
		</div>
		<p class="combined-uncertainty">
			<strong>Combined Standard Uncertainty (uc):</strong> 1.5%<br>
			<strong>Expanded Uncertainty (U, k=2):</strong> 3.0%
		</p>
	</div>
</div>

<style>
	.uncertainty-statement-section {
		margin-bottom: 1.5rem;
	}

	.uncertainty-statement-section h3 {
		font-size: 1.1rem;
		font-weight: bold;
		margin-bottom: 1rem;
		text-decoration: underline;
	}

	.uncertainty-content {
		font-size: 0.9rem;
		line-height: 1.5;
	}

	.uncertainty-content p {
		margin-bottom: 1rem;
		text-align: justify;
	}

	.uncertainty-table {
		margin: 1rem 0;
	}

	table {
		width: 100%;
		border-collapse: collapse;
		font-size: 0.85rem;
	}

	th, td {
		border: 1px solid #333;
		padding: 0.4rem;
		text-align: center;
	}

	th {
		background-color: #f5f5f5;
		font-weight: bold;
	}

	.combined-uncertainty {
		background-color: #f9f9f9;
		padding: 0.75rem;
		border: 1px solid #ddd;
		border-radius: 4px;
		margin-top: 1rem;
	}
</style>