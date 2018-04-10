<template>
  <div id="app">
    <calendar></calendar>
  </div>
</template>

<script>
import calendar from './components/Calendar';

export default {
  name: 'app',
  components: {
    calendar,
  },
}
</script>

<style src="./styles/normalize.css" lang="scss"></style>