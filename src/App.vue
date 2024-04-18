<template>
  <form @submit.prevent="addTodo">
    <label for="new-todo">Add new todo</label>
    <input v-model="todoText" type="text" id="new-todo" />
    <button type="submit">add</button>
  </form>

  <form>
    <label for="filter">Filter</label>
    <input type="radio" name="filter" id="all" @click="filter = 'all'" />
    <input type="radio" name="filter" id="done" @click="filter = 'done'" />
    <input type="radio" name="filter" id="undone" @click="filter = 'undone'" />
  </form>

  <div class="todoList">
    <ul>
      <li v-for="todo of filterdTodos" :key="todo.id">
        <input
          type="checkbox"
          name=""
          :id="todo.id"
          v-model="todo.done"
          @change="patchTodo(todo)"
        />
        <label :for="todo.id">{{ todo.description }}</label>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  data() {
    return {
      todoText: '',
      todos: [],
      filter: 'all'
    }
  },
  computed: {
    filterdTodos() {
      switch (this.filter) {
        case 'all':
          return this.todos

        case 'done':
          return this.todos.filter((todo) => todo.done == true)

        case 'undone':
          return this.todos.filter((todo) => todo.done == false)

        default:
          return null
      }
    }
  },
  methods: {
    addTodo() {
      console.log('ich bin hier!')
      fetch('http://localhost:4730/todos', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          description: this.todoText,
          done: false
        })
      }).then(this.getAllTodos())
    },
    getAllTodos() {
      fetch('http://localhost:4730/todos')
        .then((res) => res.json())
        .then((todos) => {
          this.todos = todos
        })
    },
    patchTodo(todo) {
      fetch('http://localhost:4730/todos/' + todo.id, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ done: todo.done })
      }).then(this.getAllTodos())
    }
  },
  created() {
    this.getAllTodos()
  }
}
</script>
<style scoped></style>
