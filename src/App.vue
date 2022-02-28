<template>
  <div>
    <Todo />
  </div>
</template>

<script>
import Todo from './components/Todo.vue';
export default {
  name: 'App',
  components: { Todo },
  setup(props, { emit }) {},
};
</script>

<style>
@import './assets/base.css';
</style>
