<template>
  <ul class="todoList">
    <li class="todoList__item" v-for="todo in todos" :key="todo.id">
      <TodoListItem
        :todo="todo"
        @updateTodo="$emit('updateTodo', $event)"
        @deleteTodo="$emit('deleteTodo', $event)"
      />
    </li>
  </ul>
</template>

<script>
import TodoListItem from "./TodoListItem";

export default {
  name: "TodoList",
  components: { TodoListItem },
  props: ["todos"]
};
</script>

<style lang="scss" scoped>
.todoList {
  padding: 5.2rem 0 0 0;
  margin: 0 1rem;

  &__item {
    margin: 1rem 0;
  }
}

.out-leave-active {
  transition: 0.5s;
}

.out.leave-to {
  transform: scale(1, 0);
}
</style>