<script lang="ts">
	type Todo = {
		id: string;
		text: string;
		completed: boolean;
	};

	let todos: Todo[] = [];
	let newTodoText = '';
	let editingId: string | null = null;
	let editingText = '';

	const STORAGE_KEY = 'svelte-todo-list';

	function loadTodos() {
		try {
			const stored = localStorage.getItem(STORAGE_KEY);
			if (stored) {
				todos = JSON.parse(stored);
			}
		} catch (e) {
			console.error('Failed to load todos from localStorage', e);
		}
	}

	function saveTodos() {
		try {
			localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
		} catch (e) {
			console.error('Failed to save todos to localStorage', e);
		}
	}

	function addTodo() {
		if (newTodoText.trim()) {
			todos = [...todos, { id: crypto.randomUUID(), text: newTodoText.trim(), completed: false }];
			saveTodos();
			newTodoText = '';
		}
	}

	function deleteTodo(id: string) {
		todos = todos.filter(todo => todo.id !== id);
		saveTodos();
	}

	function toggleTodo(id: string) {
		todos = todos.map(todo =>
			todo.id === id ? { ...todo, completed: !todo.completed } : todo
		);
		saveTodos();
	}

	function updateTodo(id: string, newText: string) {
		if (!newText.trim()) return;
		todos = todos.map(todo =>
			todo.id === id ? { ...todo, text: newText.trim() } : todo
		);
		saveTodos();
	}

	function startEdit(id: string, text: string) {
		editingId = id;
		editingText = text;
	}

	function cancelEdit() {
		editingId = null;
		editingText = '';
	}

	function completeEdit() {
		if (editingId && editingText.trim()) {
			updateTodo(editingId, editingText);
			cancelEdit();
		}
	}

	// Load todos on component mount
	loadTodos();

		$: todos, saveTodos();
</script>

<div class="todo-container">
	<h2>Minimalist Todo List</h2>
	
	<div class="todo-input">
		<input 
			type="text" 
			placeholder="Add a new todo..." 
			bind:value={newTodoText}
			on:keydown={(e) => e.key === 'Enter' && addTodo()}
		/>
		<button on:click={addTodo} disabled={!newTodoText.trim()}>Add</button>
	</div>
	
	{#if todos.length === 0}
		<p class="empty-message">No todos yet. Add one above!</p>
	{:else}
		<div class="todo-list">
			{#each todos as todo (todo.id)}
				<div class="todo-item {todo.completed ? 'completed' : ''}">
					<input 
						type="checkbox" 
						id="todo-{todo.id}"
						bind:checked={todo.completed}
						on:change={() => toggleTodo(todo.id)}
				/>
					<label for="todo-{todo.id}" class="todo-text" on:dblclick={() => startEdit(todo.id, todo.text)}>
						{todo.text}
					</label>
					<div class="todo-actions">
						<button 
							class="edit-btn" 
							on:click={() => startEdit(todo.id, todo.text)}
						>
							✎
						</button>
						<button 
							class="delete-btn" 
							on:click={() => deleteTodo(todo.id)}
						>
							×
						</button>
					</div>
					
					{#if editingId === todo.id}
						<div class="edit-mode">
							<input 
								type="text" 
								bind:value={editingText}
								on:keydown={(e) => e.key === 'Enter' && completeEdit()}
								on:blur={completeEdit}
							/>
							<button class="save-btn" on:click={completeEdit}>Save</button>
							<button class="cancel-btn" on:click={cancelEdit}>Cancel</button>
						</div>
					{/if}
				</div>
			{/each}
		</div>
	{/if}
	
	<div class="todo-stats">
		<p>Completed: {todos.filter(t => t.completed).length} of {todos.length}</p>
		<button class="clear-completed" on:click={() => {
				todos = todos.filter(t => !t.completed);
				saveTodos();
			}} disabled={todos.every(t => !t.completed)}>
			Clear Completed
		</button>
		<button class="clear-all" on:click={() => {
				todos = [];
				saveTodos();
			}} disabled={todos.length === 0}>
			Clear All
		</button>
	</div>
</div>

<style>
	.todo-container {
		max-width: 500px;
		margin: 0 auto;
		padding: 2rem;
		background: white;
		border-radius: 8px;
		box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
	}

	h2 {
		text-align: center;
		color: var(--color-theme-1);
		margin-bottom: 1.5rem;
	}

	.todo-input {
		display: flex;
		gap: 0.5rem;
		margin-bottom: 1.5rem;
	}

	.todo-input input {
		flex: 1;
		padding: 0.75rem;
		border: 2px solid var(--color-bg-2);
		border-radius: 4px;
		font-size: 1rem;
		transition: border-color 0.2s;
	}

	.todo-input input:focus {
		outline: none;
		border-color: var(--color-theme-1);
	}

	.todo-input button {
		padding: 0.75rem 1.5rem;
		background: var(--color-theme-1);
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 1rem;
		font-weight: 600;
		transition: background-color 0.2s;
	}

	.todo-input button:hover:not(:disabled) {
		background: var(--color-theme-2);
	}

	.todo-input button:disabled {
		background: var(--color-bg-2);
		cursor: not-allowed;
		opacity: 0.6;
	}

	.empty-message {
		text-align: center;
		color: var(--color-text);
		font-style: italic;
		opacity: 0.7;
		margin: 1.5rem 0;
	}

	.todo-list {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		margin-bottom: 1.5rem;
	}

	.todo-item {
		display: flex;
		align-items: center;
		padding: 0.75rem;
		background: var(--color-bg-1);
		border-radius: 4px;
		transition: all 0.2s;
		border: 1px solid transparent;
	}

	.todo-item:hover {
		background: var(--color-bg-2);
	}

	.todo-item.completed {
		opacity: 0.7;
	}

	.todo-item.completed .todo-text {
		text-decoration: line-through;
		color: var(--color-text);
	}

	.todo-item input[type="checkbox"] {
		margin-right: 0.75rem;
		width: 1.2rem;
		height: 1.2rem;
		cursor: pointer;
	}

	.todo-text {
		flex: 1;
		font-size: 1rem;
		cursor: pointer;
		padding: 0.25rem 0;
		border-radius: 2px;
		transition: background-color 0.2s;
	}

	.todo-text:hover {
		background: rgba(0, 0, 0, 0.05);
	}

	.todo-actions {
		display: flex;
		gap: 0.25rem;
	}

	.edit-btn, .delete-btn {
		width: 2rem;
		height: 2rem;
		border: none;
		border-radius: 4px;
		background: transparent;
		cursor: pointer;
		font-size: 1.2rem;
		font-weight: 600;
		transition: all 0.2s;
	}

	.edit-btn:hover {
		background: var(--color-bg-2);
		color: var(--color-theme-2);
	}

	.delete-btn:hover {
		background: rgba(255, 0, 0, 0.1);
		color: red;
	}

	.edit-mode {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		flex: 1;
	}

	.edit-mode input {
		flex: 1;
		padding: 0.5rem;
		border: 2px solid var(--color-theme-2);
		border-radius: 4px;
	}

	.edit-mode input:focus {
		outline: none;
		border-color: var(--color-theme-1);
	}

	.save-btn, .cancel-btn {
		padding: 0.5rem 1rem;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 0.9rem;
		font-weight: 600;
		transition: background-color 0.2s;
	}

	.save-btn {
		background: var(--color-theme-2);
		color: white;
	}

	.save-btn:hover {
		background: var(--color-theme-1);
	}

	.cancel-btn {
		background: var(--color-bg-2);
		color: var(--color-text);
	}

	.cancel-btn:hover {
		background: #ddd;
	}

	.todo-stats {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding-top: 1.5rem;
		border-top: 1px solid var(--color-bg-2);
	}

	.todo-stats p {
		margin: 0;
		color: var(--color-text);
		font-size: 0.9rem;
	}

	.clear-completed, .clear-all {
		padding: 0.5rem 1rem;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 0.9rem;
		font-weight: 600;
		transition: all 0.2s;
	}

	.clear-completed {
		background: var(--color-bg-2);
		color: var(--color-text);
	}

	.clear-completed:hover:not(:disabled) {
		background: #ddd;
	}

	.clear-completed:disabled, .clear-all:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	.clear-all {
		background: rgba(255, 0, 0, 0.1);
		color: red;
	}

	.clear-all:hover:not(:disabled) {
		background: rgba(255, 0, 0, 0.2);
	}
</style>