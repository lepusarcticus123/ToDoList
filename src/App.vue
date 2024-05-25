<script setup>
import { ref, onMounted, computed, watch} from 'vue'

//定义变量
let theme=ref('normal') //定义响应式主题变量
const todos = ref([])   //存储输入的todo的中转变量
const name = ref('')    //定义用户名字的变量
const input_content = ref('') //输入的todo内容变量
const input_category = ref(null) //输入的类别变量

//计算属性，对所有todo进行排序
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))
//添加tooo函数
const addTodo = () => {
  //判断
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  //添加
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
  //清空输入框
  input_category.value=null
  input_content.value=''
}
//删除todo函数
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t)=>t !==todo)
  //创建新数组，包含所有通过测试的元素
  //强调了开发的数据不可变性
  //创建新数组虽然增加了一些处理压力，但影响非常小
}
//监视所有todos的改变，将所有todos的变化存储在本地仓库中
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})
//监视所有name的变化，将name存储到本地仓库
watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
})
//监视所有theme的变化，将theme存储到本地仓库
watch(theme, (newVal) => {
  localStorage.setItem('theme', newVal)
})
//生命周期钩子函数，从本地仓库取出我们所需要的值
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  theme.value = localStorage.getItem('theme') || 'normal'
})

//改变颜色方法
const changeColor = async (event) => {
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
      <!-- 颜色改变导航条 -->
    <div class="nav">
      <ul>
        <li @click="changeColor($event)" id="color1">Normal</li>
        <li @click="changeColor($event)" id="color2">Teal</li>
        <li @click="changeColor($event)" id="color3">Amber</li>
      </ul>
    </div>
    
    <!-- 开场问候部分 -->
    <section class="greeting">
      <h2 class="title">
        Wassup?
        <input 
          type="text" 
          id="name" 
          placeholder="name here" 
          v-model="name">
      </h2>
    </section>
    <!-- 创建todo部分 -->
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
    <!-- todo列表展示 -->
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
