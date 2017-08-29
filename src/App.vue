<template>
  <div id="app">
    <input @click="showTimePicker" v-model="beginTime" type="text" placeholder="开始日期" readonly>
    <input type="text" v-model="endTime" placeholder="结束日期" readonly>
    <date-picker v-show="showPicker" @confirm="confirm" @hide-time-picker="hideTimePicker"></date-picker>
  </div>
</template>

<script>
import datePicker from './components/datePicker'

export default {
  name: 'app',
  components: {
    datePicker
  },
  data() {
    showPicker: false
  },
  methods() {
    showTimePicker() {
      this.showPicker = true
    },
    hideTimePicker() {
      this.showPicker = false
    }
    confirm(startDate, endDate) {
      this.beginTime = startDate
      this.endTime = endDate
      this.showPicker = false
    },
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
