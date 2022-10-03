<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const todoLists = ref([])
  const userName = ref('')
  const listName = ref('')
  const listCategory = ref(null)
  const todoListsSortedAsc = computed(() => todoLists.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }))
  const createTodoList = () => {
    if (listName.value.trim() === '' || listCategory.value === null ) {
      return
    }

    todoLists.value.push({
      content: listName.value,
      category: listCategory.value,
      done: false,
      createdAt: new Date().getTime()
    })

    listName.value = ''
    listCategory.value = null
  }
  const removeTodo = todo => {
    todoLists.value = todoLists.value.filter(t => t !== todo)
  }

  watch(todoLists, newVal => {
    localStorage.setItem('todoLists', JSON.stringify(newVal))
  }, { deep: true })

  watch(userName, (newVal) => {
    localStorage.setItem('userName', newVal)
  })

  onMounted(() => {
    userName.value = localStorage.getItem('userName') || ''
    todoLists.value = JSON.parse(localStorage.getItem('todoLists')) || []
  })
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, 
        <input type="text" placeholder="Name here" v-model="userName" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO LIST</h3>
      <form @submit.prevent="createTodoList">
        <h4>Give a proper name for your list...</h4>
        <input 
            type="text" 
            placeholder="e.g. Shopping List #1"
            v-model="listName" />
        <h4>Pick a category</h4>
        <div class="options">
            <label>
                <input 
                    type="radio" 
                    name="category" 
                    value="business"
                    v-model="listCategory" />
                <span class="bubble business"></span>
                <div>Business</div>
            </label>

            <label>
                <input 
                    type="radio" 
                    name="category" 
                    value="personal"
                    v-model="listCategory" />
                <span class="bubble personal"></span>
                <div>Personal</div>
            </label>

            <label>
                <input 
                    type="radio" 
                    name="category" 
                    value="shopping"
                    v-model="listCategory" />
                <span class="bubble shopping"></span>
                <div>Shopping</div>
            </label>

            <label>
                <input 
                    type="radio" 
                    name="category" 
                    value="cook"
                    v-model="listCategory" />
                <span class="bubble cook"></span>
                <div>Cook</div>
            </label>

        </div>
        <input type="submit" value="Create todo list" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LISTS</h3>
      <div class="list">
        <div v-for="todo in todoListsSortedAsc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
 