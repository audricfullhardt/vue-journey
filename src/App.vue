<template>
    <div class="todo-container">
        <h1>Todo List</h1>
        <div class="add-todo">
            <input 
                type="text" 
                v-model="newTodo" 
                @keyup.enter="addTodo"
                placeholder="Ajouter une tâche" 
            />
            <button @click="addTodo" :disabled="!newTodo">
                <span>Ajouter</span>
            </button>
        </div>

        <div class="filters">
            <button 
                v-for="filter in filters" 
                :key="filter"
                @click="currentFilter = filter"
                :class="{ active: currentFilter === filter }"
            >
                {{ filter }}
            </button>
        </div>

        <ul>
            <li v-for="todo in filteredTodos" :key="todo.id" :class="{ completed: todo.completed }">
                <div class="todo-content">
                    <input 
                        type="checkbox" 
                        v-model="todo.completed"
                        class="todo-checkbox"
                    />
                    <span v-if="editingTodoId !== todo.id" @dblclick="startEdit(todo)">
                        {{ todo.text }}
                    </span>
                    <input 
                        v-else
                        type="text" 
                        v-model="todo.text" 
                        @blur="finishEdit(todo)"
                        @keyup.enter="finishEdit(todo)"
                        @keyup.esc="cancelEdit"
                        ref="editInput"
                        class="edit-input"
                    />
                </div>
                <div class="todo-actions">
                    <button @click="editTodo(todo.id)" class="edit-btn">
                        ✏️
                    </button>
                    <button @click="deleteTodo(todo.id)" class="delete-btn">
                        🗑️
                    </button>
                </div>
            </li>
        </ul>

        <div class="todo-stats" v-if="todos.length">
            <span>{{ remainingTodos }} tâches restantes</span>
            <button @click="clearCompleted" v-if="completedTodos > 0">
                Supprimer les tâches terminées
            </button>
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const newTodo = ref('')
const todos = ref([])
const editingTodoId = ref(null)
const filters = ['Toutes', 'Actives', 'Terminées']
const currentFilter = ref('Toutes')
const originalTodo = ref('')

const filteredTodos = computed(() => {
    switch(currentFilter.value) {
        case 'Actives':
            return todos.value.filter(todo => !todo.completed)
        case 'Terminées':
            return todos.value.filter(todo => todo.completed)
        default:
            return todos.value
    }
})

const remainingTodos = computed(() => 
    todos.value.filter(todo => !todo.completed).length
)

const completedTodos = computed(() => 
    todos.value.filter(todo => todo.completed).length
)

function addTodo() {
    if (!newTodo.value.trim()) return
    todos.value.push({ 
        id: Date.now(),
        text: newTodo.value.trim(),
        completed: false
    })
    newTodo.value = ''
}

function deleteTodo(id) {
    todos.value = todos.value.filter(todo => todo.id !== id)
}

function editTodo(id) {
    const todo = todos.value.find(t => t.id === id)
    if (todo) {
        originalTodo.value = todo.text
        editingTodoId.value = id
        setTimeout(() => {
            document.querySelector('.edit-input')?.focus()
        })
    }
}

function startEdit(todo) {
    originalTodo.value = todo.text
    editingTodoId.value = todo.id
}

function finishEdit(todo) {
    if (!todo.text.trim()) {
        todo.text = originalTodo.value
    }
    editingTodoId.value = null
}

function cancelEdit() {
    const todo = todos.value.find(t => t.id === editingTodoId.value)
    if (todo) {
        todo.text = originalTodo.value
    }
    editingTodoId.value = null
}

function clearCompleted() {
    todos.value = todos.value.filter(todo => !todo.completed)
}
</script>

<style>
body {
    font-family: Arial, sans-serif;
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    background-color: black;
}

.todo-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
}

h1 {
    color: #42b983;
    text-align: center;
    margin-bottom: 30px;
}

.add-todo {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

input[type="text"] {
    width: 100%;
    padding: 12px;
    border: 2px solid #eee;
    border-radius: 6px;
    font-size: 16px;
    transition: border-color 0.3s;
}

input[type="text"]:focus {
    border-color: #42b983;
    outline: none;
}

.filters {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

button {
    padding: 12px 20px;
    background-color: #42b983;
    color: white;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    transition: background-color 0.3s;
    font-size: 14px;
}

button:hover:not(:disabled) {
    background-color: #3aa876;
}

button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
}

button.active {
    background-color: #3aa876;
    font-weight: bold;
}

.edit-btn, .delete-btn {
    padding: 6px 12px;
    font-size: 12px;
}

ul {
    list-style-type: none;
    padding: 0;
    margin-top: 20px;
}

li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px;
    background-color: #f8f9fa;
    margin-bottom: 8px;
    border-radius: 6px;
    border-left: 4px solid #42b983;
    transition: all 0.3s;
    color: black;
}

li.completed {
    border-left-color: gray;
    opacity: 0.7;
}

li.completed .todo-content {
    text-decoration: line-through;
    color: #666;
}

.todo-content {
    display: flex;
    align-items: center;
    gap: 10px;
    flex: 1;
}

.todo-actions {
    display: flex;
    gap: 8px;
}

.todo-checkbox {
    width: 18px;
    height: 18px;
    cursor: pointer;
}

.todo-stats {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 20px;
    padding-top: 20px;
    border-top: 1px solid #eee;
    color: #666;
}

.edit-input {
    flex: 1;
}
</style>