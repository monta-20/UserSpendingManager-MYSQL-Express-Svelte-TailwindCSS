<script lang="ts">
	import { onMount } from 'svelte';
  
	let spendings: any = [];
	let filteredSpendings: any = [];
	let loading: boolean = true;
	let error: any = null;
  
	// Filter states
	let userId = '';
	let startDate = '';
	let endDate = '';
	let spendingType = '';
	let model = '';
  
	onMount(async () => {
	  await fetchSpendings(); // Initial fetch
	});
  
	// Function to fetch spendings from the backend
	async function fetchSpendings() {
	  try {
		// Construct the query parameters
		const queryParams = new URLSearchParams({
		  ...(userId && { userid: userId }),
		  ...(startDate && { startDate }),
		  ...(endDate && { endDate }),
		  ...(spendingType && { type: spendingType }),
		  ...(model && { model }),
		}).toString();
  
		const response = await fetch(`http://localhost:4000/spendings?${queryParams}`);
		if (!response.ok) {
		  throw new Error('Failed to fetch data');
		}
		const result = await response.json();
		spendings = result.data; // Assuming the data is inside a 'data' property
		filteredSpendings = spendings; // Initialize filteredSpendings
	  } catch (err: any) {
		error = err.message;
	  } finally {
		loading = false;
	  }
	}
  
	// Function to filter spendings based on input criteria
	function filterSpendings() {
	  // Call the fetch function with the current filter states
	  fetchSpendings();
	}
</script>

<main>
	<h1>Spendings</h1>
  
	{#if loading}
	  <p>Loading...</p>
	{:else if error}
	  <p>Error: {error}</p>
	{:else}
	  <div class="filters">
		<h2>Filters</h2>
		<label>
		  User ID:
		  <input type="text" bind:value={userId} placeholder="Enter User ID" />
		</label>
		<label>
		  Start Date:
		  <input type="date" bind:value={startDate} />
		</label>
		<label>
		  End Date:
		  <input type="date" bind:value={endDate} />
		</label>
		<label>
		  Type:
		  <input type="text" bind:value={spendingType} placeholder="Enter Type (e.g., Food)" />
		</label>
		<label>
		  Model:
		  <input type="text" bind:value={model} placeholder="Enter Model (e.g., Credit Card)" />
		</label>
		<!-- Add Filter Button -->
		<button on:click={filterSpendings}>Filter</button>
	  </div>
  
	  {#if filteredSpendings.length > 0}
		<h2>Detailed Spending Records</h2>
		<table>
		  <thead>
			<tr>
			  <th>User ID</th>
			  <th>Amount</th>
			  <th>Date</th>
			  <th>Type</th>
			  <th>Model</th>
			</tr>
		  </thead>
		  <tbody>
			{#each filteredSpendings as spending}
			  <tr>
				<td>{spending.userid}</td>
				<td>{spending.count}</td>
				<td>{new Date(spending.createdat).toLocaleDateString()}</td>
				<td>{spending.type}</td>
				<td>{spending.model}</td>
			  </tr>
			{/each}
		  </tbody>
		</table>
	  {:else}
		<p>No spendings found.</p>
	  {/if}
	{/if}
</main>

<style>
	/* Global styles for the page */
	main {
		font-family: Arial, sans-serif;
		margin: 20px;
	}

	h1 {
		color: #333;
	}

	.filters {
		margin-bottom: 20px;
		padding: 15px;
		border: 1px solid #ddd;
		border-radius: 5px;
		background-color: #f9f9f9;
	}

	label {
		display: block;
		margin: 10px 0;
	}

	input[type="text"],
	input[type="date"] {
		width: 100%;
		padding: 8px;
		margin-top: 5px;
		border: 1px solid #ccc;
		border-radius: 4px;
	}

	button {
		margin-top: 10px;
		padding: 10px 15px;
		color: #fff;
		background-color: #007bff;
		border: none;
		border-radius: 4px;
		cursor: pointer;
	}

	button:hover {
		background-color: #0056b3;
	}

	table {
		width: 100%;
		border-collapse: collapse;
		margin-top: 20px;
	}

	th, td {
		padding: 10px;
		text-align: left;
		border-bottom: 1px solid #ddd;
	}

	th {
		background-color: #f2f2f2;
	}

	tr:hover {
		background-color: #f1f1f1;
	}
</style>
