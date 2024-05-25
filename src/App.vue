<script setup>
import { ref, onMounted, computed, watch,nextTick} from 'vue'

const todos = ref([])
let theme=ref('normal')
const name = ref('')
const input_content = ref('')
const input_category = ref(null)
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))
const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
  input_category.value=null
  input_content.value=''
}
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t)=>t !==todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})
watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
})
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

//改变颜色方法
const changeColor = async (event) => {
  await nextTick(); // 等待下次 DOM 更新
  if (event.target.id === 'color1') {
      theme.value = 'normal'
    }
  else if(event.target.id === 'color2'){
    theme.value = 'teal'
  }else if(event.target.id === 'color3'){
    theme.value = 'amber'
  }
};
</script>

<template>
  <main class="app">
    <div :class="theme">
    <div class="nav">
      <ul>
        <li @click="changeColor($event)" id="color1">Normal</li>
        <li @click="changeColor($event)" id="color2">Teal</li>
        <li @click="changeColor($event)" id="color3">Amber</li>
      </ul>
    </div>
    
      <section class="greeting">
      <h2 class="title">
        GET READY?<input type="text" id="name" placeholder="name here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>Get start by a todo first</h4>
        <input type="text" name="content" id="content" placeholder="e.g. do my homework" v-model="input_content" />
        <h4>PICK A CATEGORY</h4>
        <div class="options">
          <label for="bussiness">
            <input type="radio" name="category" value="bussiness" id="bussiness" v-model="input_category">
            <span class="bubble bussiness"></span>
            <div>Bussiness</div>
          </label>
          <label for="personal">
            <input type="radio" name="category" value="personal" id="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST BELOW</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category == 'business'
                ? 'business'
                : 'personal'
              }`"></span>
          </label>
          <div class="todo-content">
          <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
    </div>
    
  </main>
</template>
