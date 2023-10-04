<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref("");

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (index) => {
  todos.value.splice(index, 1);
};

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main
    class="min-h-screen flex flex-col justify-center items-center gap-5 max-w-md px-5 mx-auto"
  >
    <h2 class="text-center font-bold text-2xl">
      What's up,
      <input type="text" v-model="name" placeholder="name" class="w-24"/>
    </h2>
    <section class="w-full">
      <form @submit.prevent="addTodo" class="flex flex-col gap-5">
        <h4 class="font-semibold">What's you want to do?</h4>
        <input
          type="text"
          placeholder="ex. Learning Vue.js"
          v-model="input_content"
          class="p-2 rounded border-2 border-black"
        />
        <h4 class="font-semibold">Pick a category</h4>
        <div class="flex gap-5 items-center justify-center">
          <label
            for="school"
            class="bg-teal-500 p-5 rounded-lg font-semibold hover:bg-teal-500/50 text-white transition-all duration-300 ease-in-out text-center w-full"
          >
            <input
              type="radio"
              name="category"
              value="school"
              id="school"
              v-model="input_category"
            />
            School
          </label>
          <label
            for="personal"
            class="bg-cyan-500 p-5 rounded-lg font-semibold hover:bg-cyan-500/50 text-white transition-all duration-300 ease-in-out text-center w-full"
          >
            <input
              type="radio"
              name="category"
              value="personal"
              id="personal"
              v-model="input_category"
            />
            Personal
          </label>
        </div>

        <button
          type="submit"
          class="bg-blue-500 p-3 rounded-lg font-semibold text-white hover:bg-blue-700 transition-all duration-300 ease-in-out"
        >
          ADD TODO
        </button>
      </form>
    </section>
    <section class="w-full">
      <h3 class="mb-3 font-semibold">To do list :</h3>
      <div class="flex flex-col justify-center items-center gap-5">
        <div
          v-for="todo in todos_asc"
          :key="todo.id"
          :class="{
            'border-teal-500': todo.category === 'school',
            'border-cyan-500': todo.category === 'personal',
          }"
          class="flex justify-between items-center gap-3 border-b-2 bg-gray-50 p-2 w-full"
        >
          <div class="flex gap-3">
            <label for="">
              <input type="checkbox" name="" v-model="todo.done" id="" />
            </label>
            <div>
              <input
                type="text"
                v-model="todo.content"
                class="bg-gray-50 font-semibold"
              />
            </div>
          </div>
          <button
            class="bg-red-500 p-2 text-white my-2 hover:bg-red-600 transition-all duration-300 ease-in-out"
            @click="removeTodo(todo.id)"
          >
            Delete
          </button>
        </div>
      </div>
    </section>
  </main>
</template>
