<template>
  <div id="app">
    <start v-if="!newGame" @startGame="startGame"></start>
    <board v-if="newGame"></board>
  </div>
</template>

<script>
import Vue from 'vue'
import Start from '@/components/Start'
import Board from '@/components/Board'

Vue.component('Start', Start)
Vue.component('Board', Board)

export default {
  name: 'app',
  data() {
    return {
      newGame: false
    }
  },
  methods: {
    startGame () {
      this.newGame = true;
    },
    finGame () {
      this.newGame = false;
    } 
  }
}
</script>

<style lang="less">
@import 'assets/less/style';
</style>
