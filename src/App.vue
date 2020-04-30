<template>
  <div id="app">
    <transition name="fade">
      <div v-if="backdrop" class="backdrop" @click="toggleBackdropToChild"></div>
    </transition>
    <Header @addTodoClick="toggleBackdrop" @addTodo="addTodo" ref="header" />
    <TodoList v-if="todos.length" :todos="todos" @updateTodo="updateTodo" @deleteTodo="deleteTodo" />
    <h2 v-else>{{ message }}</h2>
  </div>
</template>

<script>
import axios from "axios";
import Header from "./components/Header";
import TodoList from "./components/TodoList";

export default {
  name: "App",
  components: { Header, TodoList },
  data() {
    return {
      API_URL: "https://parkar-todo-2.herokuapp.com/graphql",
      todos: [],
      message: "Loading...",
      backdrop: false
    };
  },
  methods: {
    addTodo(text) {
      axios
        .post(
          this.API_URL,
          {
            query: `
              mutation createTask ($text: String!){
                createTask (title: $text) {
                  task {
                    id,
                    title,
                    isCompleted
                  }
                }
              }
            `,
            variables: {
              text: text.trim()
            }
          },
          {
            headers: {
              "Content-Type": "application/json"
            }
          }
        )
        .then(res => this.todos.splice(0, 0, res.data.data.createTask.task))
        .catch(err => console.error(err));
    },
    updateTodo(todo) {
      axios
        .post(
          this.API_URL,
          {
            query: `
          mutation updateTask ($id: ID!, $isCompleted: Boolean!) {
            updateTask (id: $id, isCompleted: $isCompleted) {
              task {
                id,
                title,
                isCompleted
              }
            }
          }
        `,
            variables: {
              id: todo.id,
              isCompleted: todo.isCompleted
            }
          },
          {
            headers: {
              "Content-Type": "application/json"
            }
          }
        )
        .then(res => {
          let resTodo = res.data.data.updateTask.task;

          this.todos = this.todos.map(t => {
            if (t.id === resTodo.id) {
              t.isCompleted = resTodo.isCompleted;
            }

            return t;
          });
        })
        .catch(err => console.error(err));
    },
    deleteTodo(id) {
      axios
        .post(
          this.API_URL,
          {
            query: `
            mutation deleteTask ($id: ID!) {
              deleteTask (id: $id) {
                detail
              }
            }
        `,
            variables: {
              id: id
            }
          },
          {
            headers: {
              "Content-Type": "application/json"
            }
          }
        )
        /* eslint-disable */
        .then(_ => (this.todos = this.todos.filter(t => t.id !== id)))
        /* eslint-enable */
        .catch(err => console.error(err));
    },
    toggleBackdrop(showDialog) {
      this.backdrop = showDialog;
    },
    toggleBackdropToChild() {
      this.backdrop = !this.backdrop;
      this.$refs.header.showDialog = this.backdrop;
    }
  },
  mounted() {
    axios
      .post(
        this.API_URL,
        {
          query: `
        query {
          allTasks {
            id,
            title,
            isCompleted
          }
        }
      `
        },
        {
          headers: {
            "Content-Type": "application/json"
          }
        }
      )
      .then(res => {
        this.todos = res.data.data.allTasks;

        this.message = "No Todos";
      })
      .catch(err => {
        console.error(err);
        this.message = "Error Occured";
      });
  },
  created() {
    window.onkeyup = e => {
      if (e.keyCode === 27 && this.backdrop) {
        this.toggleBackdropToChild();
      }
    };
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,400;0,700;1,400&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Lato", sans-serif;
  list-style: none;
}

body {
  background-color: #eeeeee;
}

h2 {
  margin-top: 6rem;
  text-align: center;
}

.backdrop {
  z-index: 3;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  background-color: rgba(black, 0.5);
}
</style>
