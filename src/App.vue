<template>
  <div id="app">
    <h1>{{ header }}</h1>
    <calendar @header="header = $event"></calendar>
  </div>
</template>

<script>
import calendar from './components/Calendar';

export default {
  name: 'app',
  data() {
    return {
      header: '',
    }
  },
  components: {
    calendar,
  },
}
</script>

<style src="./styles/normalize.css" lang="css"></style>