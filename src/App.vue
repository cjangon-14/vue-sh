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

function removeTask(id) {
  tasks.value = tasks.value.filter((task) => task.id !== id)
}

const editingId = ref(null)
const editingBuffer = ref('')

function startEdit(task) {
  editingId.value = task.id
  editingBuffer.value = task.text
}

function cancelEdit() {
  editingId.value = null
  editingBuffer.value = ''
}

function finishEdit(task) {
  if (editingId.value !== task.id) {
    return
  }
  const trimmed = editingBuffer.value.trim()

  if (!trimmed) {
    removeTask(task.id)
  } else {
    task.text = trimmed
  }
}
function toggleFav(task) {
    task.favorite = !task.favorite
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
      <li
        v-for="task in tasks"
        :key="task.id"
        :class="{ done: task.completed, editing: editingId === task.id }"
      >
        <template v-if="editingId === task.id">
          <input
            type="text"
            v-model="editingBuffer"
            @keyup.enter="finishEdit(task)"
            @keydown.esc="cancelEdit"
            @blur="finishEdit(task)"
          />
          <button class="delete" @click="cancelEdit">Cancel</button>
        </template>

        <template v-else>
          <button class="delete" @click="removeTask(task.id)">X</button>
          <button class="fav" @click="toggleFav(task)">
          {{ task.favorite ? '★' : '☆' }}</button>
          <input type="checkbox" v-model="task.completed" />
          <span @click="startEdit(task)">{{ task.text }}</span>
        </template>
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
  padding: 0;
}

.task-list li {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.task-list li.done span {
  text-decoration: line-through;
  opacity: 0.6;
}

.delete {
  background: #e53e3e;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;
}
.delete:hover {
  background: #c53030;
}
.fav {
  background: transparent;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;

}
</style>
