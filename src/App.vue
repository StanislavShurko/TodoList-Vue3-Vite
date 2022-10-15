<script setup>
import {ref, computed, onMounted, watch} from "vue";

const todos = ref([]);
const name = ref('');

const inputContent = ref('');
const inputCat = ref(null);

const todosAsc = computed(() => todos.value.sort((a,b) =>{
  return a.createdAt - b.createdAt
}))

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true });

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

const addToDo = () => {
  if (inputContent.value.trim() === '' || inputContent.value.trim() === '') {
    return
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCat.value,
    isDone: false,
    createdAt: new Date().getTime(),
  })
}

const removeToDo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here..." v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create ToDo</h3>
      <form @submit.prevent="addToDo">
        <h4>What's on your ToDo list?</h4>
        <input type="text" placeholder="e.g. make video" v-model="inputContent">
        <h4>Pick a Category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="inputCat"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="inputCat"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add ToDo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.isDone && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.isDone">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeToDo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

