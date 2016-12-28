<template>
  <div id="app">
    <date-time-picker></date-time-picker>
  </div>
</template>

<script>
import dateTimePicker from './components/dateTimePicker'

export default {
  name: 'app',
  components: {
    dateTimePicker
  }
}
</script>

<style>
html, body {
  height: 100%;
  width: 100%;
  box-sizing: inherit;
  margin: 0;
  padding: 0;
  background-color: #efeff4;
}

[v-cloak]{
  display: none;
}

#app{
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
