<template>
  <div id="app">
    <!-- Conditional rendering for the header -->
    <div v-if="state === 'add'">
      <h1>Add Todo's</h1>
      <button @click="goBack">Back</button>
    </div>
    <div v-else-if="state === 'edit'">
      <h1>Edit Todo's</h1>
      <button @click="goBack">Back</button>
    </div>
    <div v-else>
      <h1>Todo's</h1>
    </div>

    <div v-if="showForms()">
      <input v-model="newTodo.name" placeholder="New Todo">
      <input v-model="newTodo.user" placeholder="User">

      <!-- Dropdown for task status -->
      <select v-model="newTodo.openStatus">
        <option :value="true">Open</option>
        <option :value="false">Closed</option>
      </select>

      <!-- Add Todo to list -->
      <button @click="addTodo">Finish</button>

      <!-- Cancel button to reset the form -->
      <button @click="goBack">Quit</button>
    </div>

    <div v-else>
      <button @click="addMode">Add</button>
      <!-- Filter buttons -->
      <div>
        <button @click="filter = 'open'">Open</button>
        <button @click="filter = 'closed'">Closed</button>
      </div>

      <!-- Search by name input -->
      <input v-model="searchQuery" placeholder="Search by name">

      <!-- Todo list -->
      <ul>
        <li v-for="(todo, index) in filteredTodos" :key="index" class="todo-item">
          <div class="task-card">
            <div>{{ todo.name }}
              <button @click="toggleStatus(index)">{{ todo.openStatus ? 'Close' : 'Reopen' }}</button>
              <button @click="editMode(todo, index)">Edit</button>
            </div>
            <div>User: {{ todo.user }}</div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      newTodo: {
          name: '',
          user: '',
          openStatus: true,
      },
      todos: [],
      filter: 'open',
      searchQuery: '',
      state: '',
      editIndex: 0,
    };
  },
  methods: {
    addTodo() {
      if (this.newTodo.name.trim() !== '' && this.state === 'add') {
        this.todos.push({
          name: this.newTodo.name,
          user: this.newTodo.user,
          openStatus: this.newTodo.openStatus
        });
        this.goBack();
      } else if (this.newTodo.name.trim() !== '' && this.state === 'edit') {
        this.todos[this.editIndex].name = this.newTodo.name;
        this.todos[this.editIndex].user = this.newTodo.user;
        this.todos[this.editIndex].openStatus = this.newTodo.openStatus;

        this.goBack();
      }
    },
    toggleStatus(index) {
      this.todos[index].openStatus = !this.todos[index].openStatus;
    },
    resetTodoForm() {
      this.newTodo.name = '';
      this.newTodo.user = '';
      this.newTodo.openStatus = true;
    },
    goBack() {
      this.state = '';
      this.resetTodoForm();
    },
    showForms() {
      return this.state === 'add' || this.state === 'edit';
    },
    addMode() {
      this.state = 'add';
      this.resetTodoForm();
    },
    editMode(todo, index) {
      this.state = 'edit';
      this.newTodo.name = todo.name;
      this.newTodo.user = todo.user;
      this.newTodo.openStatus = todo.openStatus;

      this.editIndex = index;
    },
  },
  computed: {
    filteredTodos() {
      return this.todos.filter(todo => {
            const nameMatches = todo.name.toLowerCase().includes(this.searchQuery.toLowerCase());
            if (this.filter === 'open') {
              return todo.openStatus && nameMatches;
            } else if (this.filter === 'closed') {
              return !todo.openStatus && nameMatches;
            } else {
              return nameMatches;
            }
          });
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.task-card {
  border: 1px solid black;
}
.todo-item {
  display: block;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
</style>
