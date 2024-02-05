<script setup>
import { ref, reactive, onMounted } from 'vue';

let todos = reactive([])
const todo = ref("")

const fetchTodos = async () => {
    try {
        //const response = await fetch('https://jsonplaceholder.typicode.com/todos/')
        const response = await fetch('https://34ae-194-44-97-82.ngrok-free.app/api/v1.0/TodoItems/')
        const data = await response.text()
        console.log(data)
        todos.value = data.map(todo => ({
            ...todo,
            isEdit: false,
        }))
    } catch (error) {
        console.error('Error fetching todos:', error);
    }
}

const addTodo = async () => {
    try {
        let newTodo = {
            userId: 1,
            title: todo.value,
            completed: false
        }

        //const response = await fetch('https://jsonplaceholder.typicode.com/todos/',
        const response = await fetch('https://34ae-194-44-97-82.ngrok-free.app/api/v1.0/TodoItems/',
            {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(newTodo)
                , // body data type must match "Content-Type" header
            })
        const data = await response.json()
        newTodo = {
            ...newTodo,
            id: data.id,
            isEdit: false,
        }
        todos.value.push(newTodo);
    } catch (error) {
        console.error('Error fetching todos:', error);
    }
}

const deleteTodo = async (todoId) => {
    try {
        //const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${todoId}`,
        const response = await fetch(`https://34ae-194-44-97-82.ngrok-free.app/api/v1.0/TodoItems/${todoId}`,
            {
                method: "DELETE",
                headers: {
                    "Content-Type": "application/json",
                },
            })
        const data = await response.json()
        console.log(response.status)
        console.log(data)
        todos.value = todos.value.filter(t => t.id !== todoId);
    } catch (error) {
        console.error('Error fetching todos:', error);
    }
}

const editTodo = async (todo) => {
    try {
        const editedTodo = {
            ...todo,
        };
        delete editedTodo.isEdit;
        console.log(editedTodo)

        //const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${editedTodo.id}`,
        const response = await fetch(`https://34ae-194-44-97-82.ngrok-free.app/api/v1.0/TodoItems/${editedTodo.id}`,
            {
                method: "PUT",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(editedTodo)
            })
        const data = await response.json()
        console.log(response.status)
        console.log(data)

        todo.isEdit = false;
        //todos.value = todos.value.filter(t => t.id !== todoId);
    } catch (error) {
        console.error('Error fetching todos:', error);
    }
}

onMounted(fetchTodos);
</script>

<template>
    <div class="container py-4 px-3 mx-auto">
        <h1>TODO LIST VUE 3 + VITE + ASP.NET WEB API</h1>
        <hr>
        <form class="todo-form">
            <div class="mb-3">
                <label for="add-button" class="form-label">Add item</label>
                <input v-model="todo" type="text" class="form-control" id="add-button" placeholder="Купити молоко...">
            </div>
            <button @click.prevent="addTodo" type="submit" class="btn btn-primary">ADD</button>
        </form>

        <ul class="todos">
            <li v-for="todo in todos.value" :key="todo.id" class="todos__item">
                <article class="todo todos__article">
                    <template v-if="todo.isEdit">
                        <form class="todo__form">
                            <div>
                                <label for="edit-button" class="form-label d-none">Edit item</label>
                                <input v-model="todo.title" type="text" class="form-control" id="edit-button">
                            </div>
                            <button @click.prevent="editTodo(todo)" type="submit" class="btn btn-primary">
                                DO
                            </button>
                        </form>
                    </template>
                    <template v-else>
                        <h3 class="todo__value">
                            <span class="todo__number">{{ todo.id }})</span>
                            {{ todo.title }}
                        </h3>
                    </template>

                    <div class="todo__actions">
                        <button @click="todo.isEdit = !todo.isEdit" class="btn btn-secondary">edit</button>
                        <button @click="deleteTodo(todo.id)" class="btn btn-danger">delete</button>
                    </div>
                </article>
            </li>
        </ul>
    </div>
</template>

<style scoped>
.todos {
    margin: 2rem 0;
    padding: 0;
    display: flex;
    gap: 1rem;
    flex-direction: column;
}

.todos__item {
    list-style-type: none;
}

.todo {}

.todos__article {
    display: flex;
    align-items: center;
    gap: 1rem;
    justify-content: space-between;
    background-color: #333;
    color: #fafafa;
    padding: 1rem 2rem;
    border: 2px solid #525252;
}

.todo__number {
    color: #e9e9e9;
    margin: 0 1rem 0 0;
}

.todo__value {
    font-size: 1rem;
    font-weight: 400;
    margin: 0;
}

.todo__actions {
    display: flex;
    column-gap: 1rem;
}

.todo__form {
    display: flex;
    column-gap: 1rem;
}
</style>
