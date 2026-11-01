<script>
	import TodoList from './TodoList.svelte';
</script>

<svelte:head>
	<title>Todos</title>
	<meta name="description" content="Minimalist todo list with full CRUD using localStorage" />
</svelte:head>

<section>
	<h1>Minimalist Todo List</h1>
	<p>A simple todo list application with full CRUD functionality using localStorage for persistence. 
		Double-click a todo to edit it, check boxes to mark as completed, and use the buttons to manage your tasks.</p>
	
	<TodoList />
	
	<div class="features">
		<h2>Features</h2>
		<ul>
			<li>Add new todos with Enter key or button</li>
			<li>Mark todos as completed</li>
			<li>Double-click todos to edit them</li>
			<li>Delete individual todos</li>
			<li>Clear completed todos</li>
			<li>Clear all todos</li>
			<li>Persistent storage using localStorage</li>
			<li>Real-time statistics showing completed/total count</li>
		</ul>
	</div>
</section>

<style>
	section {
		max-width: 800px;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		gap: 2rem;
	}

	h1 {
		text-align: center;
		color: var(--color-theme-1);
		margin-bottom: 0.5rem;
	}

	section > p {
		text-align: center;
		color: var(--color-text);
		font-size: 1.1rem;
		max-width: 600px;
		margin: 0 auto 2rem auto;
	}

	.features {
		background: var(--color-bg-1);
		padding: 1.5rem;
		border-radius: 8px;
	}

	.features h2 {
		text-align: center;
		color: var(--color-theme-2);
		margin-bottom: 1rem;
	}

	.features ul {
		list-style: none;
		padding: 0;
		margin: 0;
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 0.5rem;
	}

	.features li {
		padding: 0.75rem;
		background: white;
		border-radius: 4px;
		position: relative;
		padding-left: 2rem;
	}

	.features li::before {
		content: '✓';
		position: absolute;
		left: 0.75rem;
		color: var(--color-theme-2);
		font-weight: 600;
	}
</style>