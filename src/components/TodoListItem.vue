<template>
  <div class="todoItem" @click="closeDropdown">
    <div class="todoItem__title">
      <input
        class="todoItem__checkbox"
        type="checkbox"
        :checked="todo.isCompleted"
        :id="'todo-' + todo.id"
        @change="$emit('updateTodo', { id: todo.id, isCompleted: !todo.isCompleted })"
      />
      <label :for="'todo-' + todo.id" class="todoItem__label">{{ todo.title }}</label>
    </div>
    <!-- @click="showDropdown = !showDropdown" -->
    <a class="todoItem__menu" :data-dropdown="showDropdown" @click="toggleDropdown">
      <transition name="slide-fade">
        <ul v-if="showDropdown">
          <li class="delete" @click="$emit('deleteTodo', todo.id)">Delete</li>
        </ul>
      </transition>
    </a>
  </div>
</template>

<script>
export default {
  name: "TodoListItem",
  props: ["todo"],
  data: () => ({
    showDropdown: false,
    disableParentClick: false
  }),
  methods: {
    toggleDropdown() {
      this.disableParentClick = true;
      this.showDropdown = !this.showDropdown;
    },
    closeDropdown() {
      if (!this.disableParentClick && this.showDropdown) {
        this.showDropdown = !this.showDropdown;
      } else {
        this.disableParentClick = false;
      }
    }
  },
  watch: {
    todo(t) {
      this.todo = t;
    }
  },
  mounted() {
    this.checked = this.todo.isCompleted;
  }
};
</script>

<style lang="scss" scoped>
@import "../config";
.todoItem {
  height: 5rem;
  width: 100%;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
  background-color: $light-color;

  display: grid;
  grid-template-columns: auto 4rem;
  align-items: center;

  &__title {
    display: inline-block;

    input {
      display: none;
    }

    input:checked {
      & + label {
        font-style: italic;
        text-decoration: line-through;
        color: rgba(black, 0.6);
      }

      & + label::before {
        background-color: $primary-light-color;
      }
    }

    label {
      position: relative;
      margin-left: 4rem;

      &::before {
        position: absolute;
        top: -1.3rem;
        left: -3.7rem;

        content: url("../assets/check.svg");
        display: inline-block;
        width: 1.5rem;
        height: 1.5rem;
        margin: 1em;

        border: 2px solid $primary-light-color;
        border-radius: 3px;

        transition: background-color 0.1s ease-in-out;
      }
    }
  }

  &__menu {
    position: relative;

    height: 2.1rem;
    width: 2.1rem;

    background-image: url("../assets/menu.svg");
    background-repeat: no-repeat;
    background-size: contain;

    justify-self: center;

    ul {
      position: absolute;
      top: 3rem;
      left: -1.7rem;

      padding: 0.8rem 1.2rem;
      box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
      border-radius: 10px;
      background-color: $light-color;

      transition: 0.1s;

      .delete {
        cursor: pointer;
        color: red;
      }
    }
  }
}

.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.1s ease-in;
}

.slide-fade-enter,
.slide-fade-leave-to {
  transform: translateY(-10px);
  opacity: 0;
}
</style>