<template>
  <section class="todoapp">
    <!-- header -->
    <header class="header">
      <input class="new-todo" autofocus autocomplete="off" placeholder="TODO LIST (input sth here)" @keyup.enter="addTodo">

    </header>
    <!-- main section -->
    <section class="main" v-show="todos.length">
      <input class="toggle-all" id="toggle-all" type="checkbox" :checked="allChecked" @change="toggleAll({ done: !allChecked })">
      <label for="toggle-all"></label>
      <ul class="todo-list">
        <todo @toggleTodo='toggleTodo' @editTodo='editTodo' @deleteTodo='deleteTodo' v-for="(todo, index) in filteredTodos" :key="index"
          :todo="todo"></todo>
      </ul>
    </section>
    <!-- footer -->
    <footer class="footer" v-show="todos.length">
      <span class="todo-count">
        <strong>{{ remaining }}</strong>
        {{ remaining | pluralize('item') }} left
      </span>
      <ul class="filters">
        <li v-for="(val, key) in filters" :key="key">
          <a :class="{ selected: visibility === key }" @click.prevent="visibility = key">{{ key | capitalize }}</a>
        </li>
      </ul>
      <button class="clear-completed" v-show="todos.length > remaining" @click="clearCompleted">
        Clear completed
      </button>
    </footer>
  </section>
</template>

<script>
import Todo from './Todo.vue'

const STORAGE_KEY = 'todos'
const filters = {
  all: todos => todos,
  active: todos => todos.filter(todo => !todo.done),
  completed: todos => todos.filter(todo => todo.done)
}
const defalutList = [
{ text: '吾日七省吾身', done: false },
  { text: 'star this repository  (给这个项目点星了吗？)', done: false },
  { text: 'fork this repository  (fork这个项目了吗？)', done: false },
  { text: 'follow author         (关注这个作者了吗？)', done: false },
  { text: 'read blogs            (阅读教程了吗？)', done: false },
  { text: 'use and learn         (学到新东西了吗？)', done: false },
  { text: 'make a cool project?  (搞了一个牛项目吗？)', done: false },
   { text: 'are you a single dog?(是不是女朋友丢了？)', done: false },
]
export default {
  components: { Todo },
  data() {
    return {
      visibility: 'all',
      filters,
      todos: JSON.parse(window.localStorage.getItem(STORAGE_KEY)) || defalutList
    }
  },
  computed: {
    allChecked() {
      return this.todos.every(todo => todo.done)
    },
    filteredTodos() {
      return filters[this.visibility](this.todos)
    },
    remaining() {
      return this.todos.filter(todo => !todo.done).length
    }
  },
  methods: {
    setLocalStorgae() {
      window.localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos))
    },
    addTodo(e) {
      const text = e.target.value
      if (text.trim()) {
        this.todos.push({
          text,
          done: false
        })
        this.setLocalStorgae()
      }
      e.target.value = ''
    },
    toggleTodo(val) {
      val.done = !val.done
      this.setLocalStorgae()
    },
    deleteTodo(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1)
      this.setLocalStorgae()
    },
    editTodo({ todo, value }) {
      todo.text = value
      this.setLocalStorgae()
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.done)
      this.setLocalStorgae()
    },
    toggleAll({ done }) {
      this.todos.forEach(todo => {
        todo.done = done
        this.setLocalStorgae()
      })
    }
  },
  filters: {
    pluralize: (n, w) => n === 1 ? w : w + 's',
    capitalize: s => s.charAt(0).toUpperCase() + s.slice(1)
  }
}
</script>

<style lang="css">
  @import './index.css';
</style>