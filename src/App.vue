<script setup lang="ts">
import { computed, onMounted, ref, watch } from "vue";
interface Todos {
  content: string;
  category: string;
  done: boolean;
  createdAt: number;
}

const name = ref("");
const todos = ref<Todos[]>([]);
const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

const remove_toDo = (todo: Todos) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  const storedTodos = localStorage.getItem("todos");
  todos.value = storedTodos ? JSON.parse(storedTodos) : [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hey, <input type="text" placeholder="Here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>Whats on your todo list</h4>
        <input
          type="text"
          placeholder="Write something"
          v-model="input_content"
        />

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category_2"
              value="buisness"
              v-model="input_category"
            />
            <span class="bubble buisness"></span>
            <div>Buisness</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category_1"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>ToDo List</h3>

      <div class="list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="remove_toDo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
