<template>
  <div class="container">
    <div class="todo-app">
      <div class="header">
        <h1>📝 Basic Vue To-Do List</h1>
        <button class="refresh-btn" @click="resetTodos" title="Reset List">🔄</button>
      </div>

      <form @submit.prevent="addTodo" class="form">
        <input
          v-model="newTodo"
          type="text"
          placeholder="Enter a task"
          class="input"
        />
        <button type="submit" class="add-btn">Add</button>
      </form>

      <div class="filters">
        <button @click="filter = 'all'" :class="{ active: filter === 'all' }">All</button>
        <button @click="filter = 'active'" :class="{ active: filter === 'active' }">Active</button>
        <button @click="filter = 'completed'" :class="{ active: filter === 'completed' }">Completed</button>
      </div>

      <ul class="todo-list">
        <li v-for="(todo, index) in filteredTodos" :key="index" class="todo-item">
          <label class="checkbox-wrapper">
            <input type="checkbox" v-model="todo.done" @change="saveTodos" />
            <span :class="{ done: todo.done }" class="todo-text">{{ todo.text }}</span>
          </label>
          <button @click="deleteTodo(index)" class="delete-btn">✖</button>
        </li>
      </ul>

      <div class="footer">
        <div class="counts">
          <p><strong>Total:</strong> {{ totalCount }}</p>
          <p><strong>Active:</strong> {{ activeCount }}</p>
          <p><strong>Completed:</strong> {{ completedCount }}</p>
        </div>
        <button @click="clearCompleted" class="clear-btn" :disabled="completedCount === 0">
          Clear Completed
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const newTodo = ref('')
const todos = ref([])
const filter = ref('all')

// Add new task
const addTodo = () => {
  if (newTodo.value.trim() !== '') {
    todos.value.push({ text: newTodo.value, done: false })
    newTodo.value = ''
    saveTodos()
  }
}

// Delete task
const deleteTodo = (index) => {
  todos.value.splice(index, 1)
  saveTodos()
}

// Clear completed tasks
const clearCompleted = () => {
  todos.value = todos.value.filter(todo => !todo.done)
  saveTodos()
}

// Reset everything
const resetTodos = () => {
  todos.value = []
  filter.value = 'all'
  localStorage.removeItem('vue-todos')
}

// Filtered view
const filteredTodos = computed(() => {
  if (filter.value === 'completed') return todos.value.filter(t => t.done)
  if (filter.value === 'active') return todos.value.filter(t => !t.done)
  return todos.value
})

// Counts
const totalCount = computed(() => todos.value.length)
const completedCount = computed(() => todos.value.filter(t => t.done).length)
const activeCount = computed(() => todos.value.filter(t => !t.done).length)

// Save/load
const saveTodos = () => {
  localStorage.setItem('vue-todos', JSON.stringify(todos.value))
}

onMounted(() => {
  const saved = localStorage.getItem('vue-todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  }
})
</script>

<style>
body {
  margin: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f4f6f8;
}

.container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding-top: 60px;
  min-height: 100vh;
}

.todo-app {
  background-color: #fff;
  padding: 30px;
  width: 100%;
  max-width: 500px;
  border-radius: 10px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.refresh-btn {
  font-size: 20px;
  background: none;
  border: none;
  cursor: pointer;
  color: #666;
  transition: transform 0.2s, color 0.3s;
}

.refresh-btn:hover {
  transform: rotate(180deg);
  color: #007bff;
}

.todo-app h1 {
  margin: 0;
  font-size: 24px;
  color: #333;
}

.form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 2px solid #ddd;
  border-radius: 5px;
  outline: none;
}

.input:focus {
  border-color: #007bff;
}

.add-btn {
  padding: 10px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  font-weight: bold;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

.add-btn:hover {
  background-color: #0056b3;
}

.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 8px;
  border-bottom: 1px solid #e0e0e0;
}

.checkbox-wrapper {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
}

.todo-text {
  font-size: 16px;
}

.todo-text.done {
  text-decoration: line-through;
  color: #888;
}

.delete-btn {
  background: transparent;
  border: none;
  color: #dc3545;
  font-size: 18px;
  cursor: pointer;
  transition: color 0.3s;
}

.delete-btn:hover {
  color: #a71d2a;
}

.footer {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 10px;
}

.counts {
  display: flex;
  flex-direction: column;
  gap: 5px;
  font-size: 14px;
  color: #444;
}

.clear-btn {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 5px;
  cursor: pointer;
}

.clear-btn:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.filters {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
  justify-content: center;
}

.filters button {
  padding: 6px 12px;
  border: 1px solid #ddd;
  background-color: white;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s;
}

.filters button.active {
  background-color: #007bff;
  color: white;
  border-color: #007bff;
}
</style>
