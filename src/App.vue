<template>
    <div>
        <h1>Todo List</h1>
        <input type="text" v-model="newTodo" placeholder="Ajouter une tâche" />
        <button @click="addTodo">Ajouter</button>
        <ul>
            <li v-for="todo in todos" :key="todo.id">
                {{ todo.text }} 
                <button @click="deleteTodo(todo.id)">Supprimer</button>
                <button @click="editTodo(todo.id)">Modifier</button>
                <input type="text" v-model="todo.text" placeholder="Modifier la tâche" v-if="editingTodoId === todo.id" />
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref } from 'vue'
const newTodo = ref('')
const todos = ref([])
const editingTodoId = ref(null)

function addTodo() {
    todos.value.push({ text: newTodo.value })
    newTodo.value = ''
}
function deleteTodo(id) {
    todos.value = todos.value.filter(todo => todo.id !== id)
}
function editTodo(id) {
    editingTodoId.value = id
}
</script>

<style>
body {
    font-family: Arial, sans-serif;
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
}

h1 {
    color: white;
    text-align: center;
}

input[type="text"] {
    width: 70%;
    padding: 10px;
    margin-right: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

button {
    padding: 10px 20px;
    background-color: #42b983;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background-color: #3aa876;
}

ul {
    list-style-type: none;
    padding: 0;
    margin-top: 20px;
}

li {
    padding: 10px;
    background-color: #f8f9fa;
    margin-bottom: 8px;
    border-radius: 4px;
    border-left: 4px solid #42b983;
    color: black;
}

</style>