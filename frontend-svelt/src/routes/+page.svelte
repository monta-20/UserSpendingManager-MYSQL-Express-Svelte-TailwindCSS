<script lang="ts">
	import { onMount } from 'svelte';
  import Header from './Header.svelte';
  
	let spendings: any[] = [];
	let loading: boolean = true;
	let error: string | null = null;
  
	onMount(async () => {
	  try {
		const response = await fetch('http://localhost:4000/spendings');
		if (!response.ok) {
		  throw new Error('Failed to fetch data');
		}
		const result = await response.json();
		spendings = result.data; // Assuming the data is inside a 'data' property
	  } catch (err: any) {
		error = err.message;
	  } finally {
		loading = false;
	  }
	});
  </script>
  
  <main>
	
	<h1>Spendings Data</h1>
  
	{#if loading}
	  <p>Loading...</p>
	{:else if error}
	  <p>Error: {error}</p>
	{:else if spendings.length > 0}
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
		  {#each spendings as spending}
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
  </main>
  
  <style>
	/* Add styles for your table */
	table {
	  width: 100%;
	  border-collapse: collapse;
	}
	
	th, td {
	  border: 1px solid #ddd;
	  padding: 8px;
	  text-align: left;
	}
	
	th {
	  background-color: #f2f2f2;
	}
  </style>
  