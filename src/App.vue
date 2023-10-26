<script setup>
  import { ref, onMounted, computed, watch } from 'vue';
  import { Icon } from '@iconify/vue'
  
  

  const todos = ref([])
  const name = ref('')
  const input_content = ref('')
  const input_category = ref(null)

  // filtrar os todos por ordem ascendente
  const todos_asc = computed(() => todos.value.sort((a,b) => {
    return b.createdAt - a.createdAt
  }))

// ler o que é escrito no respetivo valor e coloca-lo na localstorage
  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })
  watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, {deep: true})

//Ir buscar o que foi escrito na localstorage e dar display
  onMounted(()=>{
    name.value = localStorage.getItem('name') || ""
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })

  const addTodo = ()=> {
    if (input_content.value.trim() === "" || input_category.value === null){
      return
    }
    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })
    input_content.value=""
    input_category.value=null
  }

  const removeTodo= todo => {
    todos.value = todos.value.filter (t => t !== todo)
  }
</script>

<template>
  <main class="app md:w-[700px] h-screen flex flex-col mx-auto  items-center font-mono ">
    <section class="create-todo mt-10 w-[90%] md:w-[60%] bg-white p-7 rounded-lg mb-5">
      <form @submit.prevent="addTodo" class="flex flex-col">
        <h4 class="text-center">What's on your todo list?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="input_content" class="p-2 my-2 rounded-lg text-center border-none "/>
        <h4 class="text-center my-2">Pick a category</h4>
        <div class="options flex flex-row justify-between items-center py-3">
          <label class="w-[50%] flex flex-col items-center">
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label class="w-[50%] flex flex-col items-center">
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" class="px-10 py-2 my-2 mx-auto rounded-lg bg-gradient-to-r from-yellow-200 via-green-200 to-green-500">
      </form>
    </section>
    <section class="todo-list h-screen w-[90%] md:w-[60%]">
      <h3 class="text-center mb-5 text-4xl font-bold bg-white rounded-md py-2">Todo List</h3>
      <div class="list overflow-y-auto">
        <div v-for="todo in todos_asc" :class="`flex flex-row w-[100%] justify-center mb-2 todo-item ${todo.done && 'done'}`" >
          <label class="bg-white rounded-l-md pl-3 flex">
            <input type="checkbox" class="" v-model="todo.done"/>
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content flex bg-white w-[100%]">
            <input type="text" class="px-3" v-model="todo.content"/>
          </div>
          <div class="actions">
            <button class="delete bg-white rounded-r-md py-2 pr-3" @click="removeTodo(todo)">
              <Icon icon="mdi:trash-outline"/>
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
