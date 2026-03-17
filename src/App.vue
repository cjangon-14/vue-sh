<script setup>
import { ref, computed, watch, onMounted } from 'vue'
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

const search = ref('')
const filters = ['All', 'Incomplete', 'Completed', 'Favorites']
const activeFilter = ref('All')

const filteredTasks = computed(() => {
  return tasks.value
    .filter((t) => t.text.toLowerCase().includes(search.value.toLowerCase()))
    .filter((t) => {
      if (activeFilter.value === 'Completed') return t.completed
      if (activeFilter.value === 'Incomplete') return !t.completed
      if (activeFilter.value === 'Favorites') return t.favorite
      return true
    })
})

watch(
  tasks,
  () => {
    localStorage.setItem('tasks', JSON.stringify(tasks.value))
  },
  { deep: true },
)

onMounted(() => {
  const saved = localStorage.getItem('tasks')
  if (saved){
    tasks.value =JSON.parse(saved)
  }
})
</script>

<template>
  <div class="wrapper">
    <h1>TODO</h1>
    <div class="input-row">
      <input
        type="text"
        placeholder="add new taskhere"
        v-model="newTask"
        @keydown.enter="addTask"
      />
      <button @click="addTask">Add</button>
    </div>

    <input type="text" placeholder="search here" v-model="search" />
    <div class="filters">
      <button
        v-for="filter in filters"
        :key="filter"
        :class="{ active: activeFilter === filter }"
        @click="activeFilter = filter"
      >
        {{ filter }}
      </button>
    </div>

    <ul class="task-list">
      <li
        v-for="task in filteredTasks"
        :key="task.id"
        :class="{ done: task.completed, editing: editingId === task.id }"
      >
        <template v-if="editingId === task.id">
          <input
            type="text"
            v-model="editingBuffer"
            @keyup.enter="finishEdit(task)"
            :ref="(el) => el && el.focus()"
            @keydown.esc="cancelEdit"
            @blur="finishEdit(task)"
          />
          <button class="delete" @click="cancelEdit">Cancel</button>
        </template>

        <template v-else>
          <button class="delete" @click="removeTask(task.id)">X</button>
          <button class="fav" @click="toggleFav(task)">
            {{ task.favorite ? '★' : '☆' }}
          </button>
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
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
  color: #f6c90e;
}

.fav:hover {
  transform: scale(1.2);
}

.search-input {
  width: 100%;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  margin-bottom: 1rem;
}

.filters {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
  margin-bottom: 1rem;
}

.filters button {
  padding: 0.3rem 0.8rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  background: #f0f0f0;
  cursor: pointer;
}

.filters button.active {
  background: #333;
  color: #fff;
  border-color: #333;
}
</style>
