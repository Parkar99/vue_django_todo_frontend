<template>
  <header class="header">
    <div class="header__logo">todo</div>
    <div class="header__plus" @click="showDialog  = !showDialog"></div>
    <transition name="scale">
      <section v-show="showDialog">
        <label for="add-todo">Add Todo</label>
        <input
          type="text"
          id="add-todo"
          v-model="inputText"
          @keyup.enter="addTodo"
          ref="addTodoInput"
        />
        <a class="btn-prima" @click="addTodo">add</a>
      </section>
    </transition>
  </header>
</template>

<script>
export default {
  name: "Header",
  data: () => ({
    showDialog: false,
    inputText: ""
  }),
  methods: {
    addTodo() {
      this.$emit("addTodo", this.inputText);
      this.inputText = "";
      this.showDialog = false;
    }
  },
  watch: {
    showDialog(val) {
      this.$emit("addTodoClick", val);
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../config";
.header {
  z-index: 4;

  background-color: $primary-color;
  width: 100%;
  height: 5rem;
  padding: 0 1rem;

  position: fixed;
  top: 0;
  left: 0;

  display: flex;
  align-items: center;
  justify-content: space-between;

  &__logo {
    color: $light-color;
    font-weight: bold;
    font-size: 2.2rem;
    text-transform: uppercase;
  }

  &__plus {
    height: 2.5rem;
    width: 2.5rem;
    background-image: url("../assets/add-todo.svg");
    background-size: contain;
  }

  section {
    z-index: 0;
    position: absolute;
    top: 25vh;
    left: 2em;
    right: 2em;

    padding: 2em;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
    border-radius: 10px;
    background-color: $light-color;

    label {
      display: inline-block;
      margin-bottom: 1rem;
    }

    input {
      display: block;
      width: 100%;
    }

    a {
      display: inline-block;
      margin-top: 1rem;
      padding: 0.5rem 1rem;
      color: $light-color;
      background-color: $primary-color;
      font-weight: bold;
      border-radius: 5px;

      cursor: pointer;
    }
  }
}

.scale-enter,
.scale-leave-to {
  transform: scale(0);
}

.scale-enter-active,
.scale-leave-active {
  transition: 0.5s;
}
</style>