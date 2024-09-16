<script>
    import "@fortawesome/fontawesome-free/css/all.min.css";
    import "@fortawesome/fontawesome-free/js/all";
    import { quadInOut } from "svelte/easing";
    import { blur, fly } from "svelte/transition";
    import { v4 as uuidv4 } from "uuid";

    /**
     * @typedef {Object} Todo
     * @property {string} id - Unique identifier for the todo item.
     * @property {string} text - The text content of the todo item.
     * @property {boolean} done - Indicates if the todo is marked as complete.
     */

    /**
     * @type {Todo[]} - List of todos
     */
    let todos = $state([]);

    /**
     * @type {string} - Current filter for displaying todo ('all', 'active', 'done').
     */
    let filter = $state("all");

    /**
     * Derived state to filter the todos based on the current filter.
     */
    let filteredTodos = $derived(filterTodos());

    /**
     * Derived state to count remaining (incomplete) todos.
     */
    let remainingTodos = $derived(remaining());

    /**
     * Effect hook to load todos from the local storage when component is initialized.
     */
    $effect(() => {
        const savedTodos = localStorage.getItem("todos");
        savedTodos && (todos = JSON.parse(savedTodos));
    });

    /**
     * Effect hook to save todos to local  storage whenever the todo list changes.
     */
    $effect(() => {
        localStorage.setItem("todos", JSON.stringify(todos));
    });

    /**
     * Adds a todo to the end of todo list if text string is not empty.
     *
     * @param {KeyboardEvent} event - The keydown event object.
     */
    function addTodo(event) {
        console.log(event.key);
        if (event.key !== "Enter") return;
        const todoEl = event.target;
        const id = uuidv4();
        const text = todoEl?.value.trim();
        if (!text) return;
        const done = false;
        todoEl.value = "";
        todos.push({ id, text, done });
    }

    /**
     * Deletes a todo item by filtering it out of the list.
     *
     * @param {MouseEvent} event - The click event object.
     */
    function deleteTodo(event) {
        const id = event.currentTarget?.dataset.id;
        todos = todos.filter((todo) => todo.id !== id);
    }

    /**
     * Updates the text of a specific todo item when edited.
     *
     * @param {InputEvent} event - The text input event object.
     */
    function editTodo(event) {
        const id = event.currentTarget?.dataset.id;
        const index = todos.findIndex((todo) => todo.id === id);
        todos[index].text = event.currentTarget?.value;
    }

    /**
     * Toggles the completed status of a todo item when its checkbox is clicked.
     *
     * @param {InputEvent} event - The checkbox input event object.
     */
    function toggleTodo(event) {
        const todoEl = event.target;
        const done = todoEl.checked;
        const id = todoEl.dataset.id;
        const index = todos.findIndex((todo) => todo.id === id);
        todos[index].done = done;
        todoEl.checked = !done;
    }

    /**
     * Sets the current filter for displaying todos ('all', 'active', 'done').
     *
     * @param {MouseEvent} event - The click event object.
     */
    function setFilter(event) {
        const newFilter = event.currentTarget?.value;
        filter = newFilter;
    }

    /**
     * Filters the todo list based on the current filter state.
     *
     * @returns {Todo[]} - The filtered list of todos
     */
    function filterTodos() {
        switch (filter) {
            case "active":
                return todos.filter((todo) => !todo.done);
            case "done":
                return todos.filter((todo) => todo.done);
            default:
                return todos;
        }
    }

    /**
     * Counts the number of remaining (incomplete) todos.
     *
     * @returns {number} - The number of remaining todos.
     */
    function remaining() {
        return todos.filter((todo) => !todo.done).length;
    }
</script>

<div class="container">
    <div class="todos">
        <div class="heading">
            <h1>TODO : {remainingTodos}/{todos.length}</h1>
        </div>
        {#each filteredTodos as todo, i}
            <div
                in:fly={{
                    x: -200,
                    duration: 300,
                    delay: 0 + i * 30,
                    easing: quadInOut,
                }}
                out:blur={{ duration: 250, easing: quadInOut }}
                class="todo"
            >
                <input
                    class="done-toggle"
                    onchange={toggleTodo}
                    data-id={todo.id}
                    checked={todo.done}
                    type="checkbox"
                />
                {#if todo.done}
                    <input
                        oninput={editTodo}
                        data-id={todo.id}
                        value={todo.text}
                        type="text"
                        disabled="true"
                    />
                {:else}
                    <input
                        oninput={editTodo}
                        data-id={todo.id}
                        value={todo.text}
                        type="text"
                    />
                {/if}
                <button
                    class="delete-button"
                    onclick={deleteTodo}
                    data-id={todo.id}
                >
                    <i class="fa-regular fa-square-minus"> </i></button
                >
            </div>
        {/each}
        <div class="add-todo">
            <input
                onkeydown={addTodo}
                placeholder="Add todo"
                type="text"
                enterkeyhint="done"
            />
        </div>
    </div>
    <div class="filters">
        {#each ["all", "active", "done"] as filter}
            <button onclick={setFilter} class="filter" value={filter}>
                {filter}
            </button>
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
        font-family: "Poppins", sans-serif;
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
        font-family: "Poppins", sans-serif;
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
        border: 2px solid #777777;
    }

    .delete-button:hover {
        background-color: #fa7a19;
    }
</style>
