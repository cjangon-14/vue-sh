<script setup>
import { ref } from 'vue'
const newTask = ref('')
const tasks = ref([])

function addTask() {
  const text = newTask.value.trim()
  if (!text) {
    return
  }

  tasks.value.push({
    id: Date.now(),
    text: text,
    completed: false,
    favorite: false,
  })
  newTask.value = ''
}
</script>

<template>
  <div class="wrapper">
    <h1>TODO</h1>
    <div class="input-row">
      <input type="text" placeholder="add new taskhere" v-model="newTask" />
      <button @click="addTask">Add</button>
    </div>

    <ul class="task-list">
      <li v-for="task in tasks" :key="task.id">
      <input type="checkbox" v-model="task.completed">
      <span>{{ task.text }}</span>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.app {
  max-width: 500px;
  margin: 2rem auto;
  font-family: sans-serif;
  text-align: center;
}
.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}
.input-row input {
  flex: 1;
  padding: 0.5rem;
  font-size: 1rem;
}
.input-row button {
  padding: 0.5rem 1rem;
  font-size: 1rem;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding:0;
}

.task-list li {
  display:flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.task-list li.done span{
  text-decoration:line-through;
  opacity: 0.6;
}
</style>
