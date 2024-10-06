<script lang="ts">
	import { onMount } from 'svelte';
    import Header from '../Header.svelte';
  
	let userId: number | null = null;
	let count: number | null = null;
	let spendingType: string = '';
	let model: string = '';
	let feedback: string | null = null;
  
	async function addSpending() {
	  if (userId === null || count === null || !spendingType || !model) {
		feedback = 'Please fill in all fields.';
		return;
	  }
  
	  const newSpending = {
		userid: userId,
		count,
		type: spendingType,
		model,
	  };
  
	  try {
		const response = await fetch('http://localhost:4000/spendings', {
		  method: 'POST',
		  headers: {
			'Content-Type': 'application/json',
		  },
		  body: JSON.stringify(newSpending),
		});
  
		if (!response.ok) {
		  throw new Error('Failed to add spending');
		}
  
		// Reset form fields
		userId = null;
		count = null;
		spendingType = '';
		model = '';
  
		feedback = 'Spending added successfully!';
	  } catch (error: any) {
		feedback = error.message || 'An error occurred while adding spending.';
	  }
	}
</script>

<main>
	
	<h1>Add New Spending Entry</h1>

	{#if feedback}
	  <p class="feedback">{feedback}</p>
	{/if}

	<form on:submit|preventDefault={addSpending} class="form">
	  <div class="form-group">
		<label for="userId">User ID:</label>
		<input type="number" id="userId" bind:value={userId} placeholder="Enter User ID" required />
	  </div>
	  <div class="form-group">
		<label for="count">Amount:</label>
		<input type="number" id="count" bind:value={count} placeholder="Enter Amount" required />
	  </div>
	  <div class="form-group">
		<label for="spendingType">Type:</label>
		<input type="text" id="spendingType" bind:value={spendingType} placeholder="Enter Spending Type" required />
	  </div>
	  <div class="form-group">
		<label for="model">Model:</label>
		<input type="text" id="model" bind:value={model} placeholder="Enter Payment Model" required />
	  </div>
	  <button type="submit" class="submit-button">Add Spending</button>
	</form>
</main>

<style>
	main {
		max-width: 600px;
		margin: 0 auto;
		padding: 20px;
		background-color: #fff;
		border-radius: 8px;
		box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
	}

	h1 {
		text-align: center;
		font-size: 24px;
		margin-bottom: 20px;
	}

	.feedback {
		color: green;
		text-align: center;
		margin-bottom: 10px;
	}

	.form {
		display: flex;
		flex-direction: column;
	}

	.form-group {
		margin-bottom: 15px;
	}

	label {
		display: block;
		margin-bottom: 5px;
		font-weight: bold;
	}

	input {
		width: 100%;
		padding: 10px;
		border: 1px solid #ccc;
		border-radius: 4px;
		font-size: 16px;
	}

	input:focus {
		border-color: #007BFF;
		outline: none;
	}

	.submit-button {
		padding: 10px;
		background-color: #007BFF;
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 16px;
		transition: background-color 0.3s;
	}

	.submit-button:hover {
		background-color: #0056b3;
	}
</style>
