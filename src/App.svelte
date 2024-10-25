<script>
	import { onMount } from 'svelte';
  
	let newTodo = '';
	let todos = [];
	let editingIndex = null; 
	let editedText = ''; 
  
    // Function to toggle completion status
	onMount(() => {
	  const storedTodos = localStorage.getItem('todos');
	  if (storedTodos) {
		todos = JSON.parse(storedTodos);
	  }
	});
  
    // Function to save changes to localStorage
	$: localStorage.setItem('todos', JSON.stringify(todos));
  
	// Function to add a new to-do
	function addTodo() {
	  if (newTodo.trim() !== '') {
		todos = [
		  ...todos,
		  { text: newTodo, completed: false }
		];
		newTodo = '';
	  }
	}
  
	// Function to remove a to-do
	function removeTodo(index) {
	  todos = todos.filter((_, i) => i !== index);
	}
  
	// Function to handle Enter keypress for adding to-do
	function handleKeypress(event) {
	  if (event.key === 'Enter') {
		addTodo();
	  }
	}
  
	// Function to start editing a to-do
	function startEditing(index) {
	  editingIndex = index;
	  editedText = todos[index].text;
	}
  
	// Function to finish editing a to-do
	function finishEditing(index) {
	  if (editedText.trim() !== '') {
		todos = todos.map((todo, i) =>
		  i === index ? { ...todo, text: editedText } : todo
		);
	  }
	  editingIndex = null;
	  editedText = '';
	}
  
	// Function to cancel editing (optional)
	function cancelEditing() {
	  editingIndex = null;
	  editedText = '';
	}
  </script>
  
  <h1>To-Do List App</h1>
  <p class="instruction">Double-click a task to edit it.</p>
  
  <div class="input-group">
	<input
	  type="text"
	  bind:value={newTodo}
	  placeholder="Enter a new to-do"
	  on:keypress={handleKeypress}
	/>
	<button on:click={addTodo}>Add</button>
  </div>
  
  <ul>
	{#each todos as todo, index}
	  <li>
		<input
		  type="checkbox"
		  bind:checked={todo.completed}
		/>
  
		{#if editingIndex === index}
		  <input
			type="text"
			bind:value={editedText}
			on:blur={() => finishEditing(index)}
			on:keypress={(e) => { if (e.key === 'Enter') finishEditing(index) }}
			class="edit-input"
			autofocus
		  />
		{:else}
		  <span
			class:completed={todo.completed}
			on:dblclick={() => startEditing(index)}
			title="Double-click to edit"
		  >
			{todo.text}
		  </span>
		{/if}
  
		<button on:click={() => removeTodo(index)}>Delete</button>
	  </li>
	{/each}
  </ul>
  
  <style>
	h1 {
	  text-align: center;
	  font-family: Arial, sans-serif;
	}
  
	.instruction {
	  text-align: center;
	  font-size: 0.9em;
	  color: #555;
	  margin-bottom: 1em;
	}
  
	.input-group {
	  display: flex;
	  justify-content: center;
	  margin: 1em 0;
	}
  
	input[type='text'] {
	  padding: 0.5em;
	  width: 300px;
	  font-size: 1em;
	}
  
	button {
	  padding: 0.5em 1em;
	  margin-left: 0.5em;
	  font-size: 1em;
	  cursor: pointer;
	  border: none;
	  background-color: #28a745;
	  color: white;
	  border-radius: 3px;
	  transition: background-color 0.3s ease;
	}
  
	button:hover {
	  background-color: #218838;
	}
  
	ul {
	  list-style: none;
	  padding: 0;
	  max-width: 500px;
	  margin: 0 auto;
	}
  
	li {
	  display: flex;
	  align-items: center;
	  background: #f9f9f9;
	  margin: 0.5em 0;
	  padding: 0.5em;
	  border-radius: 5px;
	  transition: background 0.3s ease;
	}
  
	li:hover {
	  background: #e9e9e9;
	}
  
	li input[type='checkbox'] {
	  margin-right: 1em;
	  width: 20px;
	  height: 20px;
	}
  
	li button {
	  margin-left: auto;
	  background: #ff4d4d;
	  color: white;
	  border: none;
	  padding: 0.3em 0.6em;
	  border-radius: 3px;
	  cursor: pointer;
	  transition: background-color 0.3s ease;
	}
  
	li button:hover {
	  background: #e60000;
	}
  
	.completed {
	  text-decoration: line-through;
	  color: green; /* Changed from gray to green */
	  cursor: pointer;
	}
  
	.edit-input {
	  flex: 1;
	  padding: 0.3em;
	  font-size: 1em;
	}
  
	/* Optional: Highlight the to-do being edited */
	li input[type='text'] {
	  border: 1px solid #ccc;
	  border-radius: 3px;
	}
  </style>
  