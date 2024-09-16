<script>
    import "@fortawesome/fontawesome-free/css/all.min.css"
    import "@fortawesome/fontawesome-free/js/all"
    import { quadIn, quadOut, quintIn, quintOut } from "svelte/easing";
    import { SvelteDate } from "svelte/reactivity";
    import { blur, fly } from "svelte/transition";

    /**
     * @type {{text: string; done: boolean}[]}
     */
    let todos = $state([]);
    let filter = $state("all");

    let filteredTodos = $derived(filterTodos());
    let remainingTodos = $derived(remaining())

    $effect (() => {
        const savedTodos = localStorage.getItem('todos')
        savedTodos && (todos = JSON.parse(savedTodos))
    })

    $effect (() => {
        localStorage.setItem('todos', JSON.stringify(todos))
    })

    /**
     * @param {{ key: string; target: any; }} event
     */
    function addTodo(event) {
        if (event.key !== "Enter") return;
        const todoEl = event.target;
        const text = todoEl.value;
        const done = false;
        todoEl.value = "";
        todos.push({ text, done });
    }

    /**
     * @param {{ target: any; }} event
     */
     function deleteTodo(event) {
        const todoEl = event.target
        const index = todoEl.dataset.index
        todos.splice(index, 1)
    }

    /**
     * @param {{ target: any; }} event
     */
    function editTodo(event) {
        const todoEl = event.target;
        const text = todoEl.value;
        const index = todoEl.dataset.index;

        todos[index].text = text;
    }

    /**
     *
     * @param {{ target: any; }} event
     */
    function toggleTodo(event) {
        const todoEl = event.target;
        const done = todoEl.checked;
        const index = todoEl.dataset.index;

        todos[index].done = done;
    }

    /**
     * @param {any} event
     */
    function setFilter(event) {
        const newFilter = event.target.value;
        console.log(newFilter);
        filter = newFilter;
    }

    function filterTodos() {
        switch (filter) {
            case "all":
                return todos;
            case "active":
                return todos.filter((todo) => !todo.done);
            case "done":
                return todos.filter((todo) => todo.done);
        }
    }

    function remaining() {
        return todos.filter(todo => !todo.done).length
    }
</script>

<div class="container">
    <div class="todos">
        <div class="heading">
            <h1>TODO : {remainingTodos}/{todos.length}</h1>
        </div>
        {#each filteredTodos as todo, i}
            <div in:fly = {{x: -200, duration: 300, delay: 0+(i*30), easing: quadOut
            }} out:blur =  {{duration: 200, easing: quadIn}} class="todo">
                <input
                    class="done-toggle"
                    onchange={toggleTodo}
                    data-index={i}
                    checked={todo.done}
                    type="checkbox"
                />
                {#if todo.done}
                    <input
                        oninput={editTodo}
                        data-index={i}
                        value={todo.text}
                        type="text"
                        disabled="true"
                    />
                {:else}
                    <input
                        oninput={editTodo}
                        data-index={i}
                        value={todo.text}
                        type="text"
                    />
                {/if}
                <button 
                class="delete-button"
                onclick={deleteTodo} data-index={i}>
                <i class="fa-regular fa-square-minus">
                </button>
            </div>
        {/each}
        <div class="add-todo">
            <input onkeydown={addTodo} placeholder="Add todo" type="text" />
        </div>
    </div>
    <div class="filters">
        {#each ["all", "active", "done"] as filter}
            <button onclick={setFilter} class="filter" value="{filter}"> {filter} </button>
        {/each}
    </div>
</div>

<style>
    :root {
        color-scheme: dark;
        accent-color: #fa7a19;
    }

    .heading {
        color: #777777;
        text-align: center;
        font-family: "Poppins", sans-serif;
        font-weight: 600;
    }

    .container {
        display: grid;
        gap: 1rem;
        min-width: 40ch;
        max-width: 70ch;
        margin: auto;
    }

    .filters {
        display: flex;
        justify-content: space-evenly;
        width: 100%;
        gap: 1rem;
        margin: auto;
        margin-block-start: 1rem;
    }

    .filter {
        flex-grow: 1;
        padding: 1rem;
        font-family: 'Poppins', sans-serif;
        font-size: medium;
        font-weight: 400;
        text-align: center;
        text-transform: capitalize;
        box-sizing: border-box;
    }

    .todos {
        display: grid;
    }

    .todo {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0.5rem 1rem;
        border: 2px solid #ccc;
        border-radius: 5px;
        box-sizing: border-box;
        margin-block-start: 1rem;
    }

    .add-todo {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0.5rem;
        border: 2px solid #ccc;
        border-radius: 5px;
        box-sizing: border-box;
        margin-block-start: 1rem;
    }

    input[type="text"] {
        font-family: 'Poppins', sans-serif;
        font-size: medium;
        font-weight: 400;
        flex-grow: 1;
        width: 100%;
        padding: 1rem;
        border: 1px solid #777777;
        border-radius: 5px;
    }

    .todo > input[type="text"] {
        margin: 0rem 1rem;
    }

    .done-toggle {
        scale: 1.5;
        flex-grow: 1;
        margin: auto;
    }

    .delete-button {
        display: flex;
        justify-content: center;
        flex-grow: 1;
        padding: 0.75rem;
        margin: auto;
        border-radius: 10px;
        border: 2px solid #777777
    }

    .delete-button:hover {
        background-color: #fa7a19;
    }
</style>
