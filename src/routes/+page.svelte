<script lang="ts">

type Todo = {
	text: string
	done: boolean
}
type Filters = 'Todos' | 'Pendientes' | 'Completas'

let todos = $state<Todo[]>([]);
let filter = $state<Filters>('Todos');
let filteredTodos = $derived(filterTodos())


$effect(()=> {
	const guardarTodos = localStorage.getItem('todos')
	guardarTodos && (todos = JSON.parse(guardarTodos))
});

$effect(()=> {
	localStorage.setItem('todos', JSON.stringify(todos))
});

function addTodo (event: KeyboardEvent) {
	if( event.key !== "Enter")
return
	const todoEl = event.target as HTMLInputElement;
	const text = todoEl.value;
	const done = false;
	todos = [...todos, {text, done}];
	todoEl.value = "";
}

function editTodo(event:Event) {
 const inputEl = event.target as HTMLInputElement;
 const index = +inputEl.dataset.index!;
 todos[index].text = inputEl.value;
}

function toggleTodo(event:Event) {
 const inputEl = event.target as HTMLInputElement;
 const index = +inputEl.dataset.index!;
 todos[index].done = !todos[index].done;
}

function setFilter(newFilter: Filters) {
	filter = newFilter;
}

function filterTodos(){
	switch (filter) {
		case 'Todos':
			return todos
		case 'Pendientes':
			return todos.filter(todo => !todo.done)
		case 'Completas':
		    return todos.filter(todo => todo.done)
	}
}

function remaining (){
	return todos.filter(todo => !todo.done).length
}

</script>

<h2 class="title">Lista de Tareas</h2>

<input onkeydown={addTodo} placeholder="Add todo" type="text">

<div class="todos">
	{#each filteredTodos as todo , i }
		<div class:activos={todo.done} class="todo">
			<input oninput={editTodo} data-index={i} type="text" value={todo.text} class="todo-text">
			<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" >
		</div>
	{/each}
</div>

<div class="filters">
	<button onclick={() => setFilter('Todos')}>Todos</button>
	<button onclick={() => setFilter('Pendientes')}>Pendientes</button>
	<button onclick={() => setFilter('Completas')}>Completas</button>
</div>
<p>{remaining()} Restantes</p>

<style>
	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}
	.todo {
		position: relative;
		transition: opacity 0.4s;
	}

	.activos {
		opacity: 0.4;
	}

	.filters {
		margin-block: 1rem;
	}

	input[type="text"] {
    width: 100%;
	padding: 1rem;
	}

	input[type="checkbox"] {
    position: absolute;
    right: 4%;
	top: 50%;
	translate: 0% -50%;
}

.title {
	margin-block-end: 2rem;
}


</style>

