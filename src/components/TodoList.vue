<template>
  <div class="todo-div">
    <h1>To-Do List App</h1>
    <input
      v-model="newTodo"
      @keyup.enter="addTodo"
      placeholder="Добавить задачи"
      class="input-todo"
    />
    <ul class="top">
      <li v-for="todo in todoStore.todos" :key="todo.id">
        <!-- Флажок для выбора задачи -->
        <input type="checkbox" v-model="selectedTasks" :value="todo.id" />
        <!-- Добавлен условный класс "completed" к тегу span -->
        <span
          :class="{ completed: todo.completed }"
          @dblclick="editTodo(todo.id)"
        >
          {{ todo.text }}
        </span>
        <button class="button-ok" @click="markAsCompleted(todo.id)">
          Выполнить
        </button>
        <button class="button-del" @click="removeTodo(todo.id)">Delete</button>
      </li>
    </ul>
    <!-- Кнопки для выполнения действий с выбранными задачами -->
    <div class="action-buttons">
      <button class="button-all-ok" @click="markSelectedAsCompleted">
        Выполнить выбранные
      </button>
      <button class="button-del-all" @click="removeSelectedTasks">
        Удалить выбранные
      </button>
      <!-- Кнопка для очистки всего списка -->
      <button
        class="button-refresh"
        @click="clearAllTasks"
        title="Очистить весь список"
      >
        &#x21bb;
      </button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import { useTodoStore } from '../stores/todoStore'

const todoStore = useTodoStore()
const newTodo = ref('')
const selectedTasks = ref<number[]>([]) // Массив для хранения ID выбранных задач

const addTodo = () => {
  if (newTodo.value.trim()) {
    todoStore.addTodo(newTodo.value.trim())
    newTodo.value = ''
  }
}

const removeTodo = (id: number) => {
  todoStore.removeTodo(id)
}

const markAsCompleted = (id: number) => {
  const todo = todoStore.todos.find((todo) => todo.id === id)
  if (todo) {
    todo.completed = true
    todoStore.saveToLocalStorage()
  }
}

const markSelectedAsCompleted = () => {
  selectedTasks.value.forEach((id) => {
    const todo = todoStore.todos.find((todo) => todo.id === id)
    if (todo) {
      todo.completed = true
    }
  })
  todoStore.saveToLocalStorage()
  selectedTasks.value = [] // Очистка массива выбранных задач
}

const removeSelectedTasks = () => {
  selectedTasks.value.forEach((id) => {
    todoStore.removeTodo(id)
  })
  selectedTasks.value = [] // Очистка массива выбранных задач
}

const clearAllTasks = () => {
  // Очищает все задачи
  todoStore.todos = []
  todoStore.saveToLocalStorage()
}

const editTodo = (id: number) => {
  const newText = prompt(
    'Edit the task:',
    todoStore.todos.find((todo) => todo.id === id)?.text
  )
  if (newText !== null && newText.trim()) {
    todoStore.editTodo(id, newText.trim())
  }
}

onMounted(() => {
  todoStore.loadFromLocalStorage()
})
</script>

<style scoped>
.completed {
  text-decoration: line-through;
  color: gray;
}

/* Основной стиль для контейнера */
.todo-div {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  border-radius: 8px;
  background-color: #c9cccf;
  box-shadow: 1px 4px 6px rgb(16, 20, 17);
}

/* Заголовок списка */
h1 {
  font-size: 24px;
  margin-bottom: 30px;
  color: #333;
  margin-top: 0;
}

/* Стили для поля ввода */
input[type='text'] {
  width: calc(100% - 22px);
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 20px;
}

/* Список задач */
ul {
  list-style-type: none;
  padding: 0;
}

/* Элементы списка */
li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #fff;
  padding: 10px;
  border-radius: 4px;
  margin-bottom: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border: dashed 0.5px black;
}

/* стили для завершенной задачи */
.completed {
  text-decoration: line-through;
  color: #888;
}

/* стили для чекбокса */
input[type='checkbox'] {
  margin-right: 10px;
  width: 20px;
  height: 30px;
}

/* Стили для кнопки выполнения */
.button-ok {
  background-color: #28a728;
  border: none;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  font-size: 14px;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}
/* Стили при наведении для кнопки выполнения */
.button-ok:hover {
  background-color: #21d13e;
}
/* Стили  для кнопки выполнения выделенных задач */
.button-all-ok {
  background-color: #28a728;
  border: none;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  font-size: 14px;
  transition: background-color 0.3s ease;
  margin-left: 0;
}
/* Стили при наведении для кнопки выполнения выделенных задач */
.button-all-ok:hover {
  background-color: #21d13e;
}
/* Стили для кнопки удаления */
.button-del {
  background-color: #9b0f0f;
  border: none;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  font-size: 14px;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}
/* Стили при наведении для кнопки удаления */
.button-del:hover {
  background-color: #db3e3e;
}
/* Стили для кнопки удаления выделенных задач */
.button-del-all {
  background-color: #9b0f0f;
  border: none;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  font-size: 14px;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}
/* Стили при наведении для кнопки удаленя выделенных задач */
.button-del-all:hover {
  background-color: #db3e3e;
}

/* Стили для кнопки очистки всего списка */
.button-refresh {
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  font-size: 18px;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}
.button-refresh:hover {
  background-color: #0056b3;
}

/* Стили для текстовых элементов */
span {
  flex-grow: 1;
  cursor: pointer;
  font-size: x-large;
}

/* Дополнительные стили для контейнера списка */
.top {
  margin-top: 20px;
}
.input-todo {
  width: 300px;
  height: 30px;
  border-radius: 5px;
}
</style>
