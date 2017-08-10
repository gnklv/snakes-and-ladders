<template>
  <div id="app">
    <div class="l-container">
      <settings 
        v-if="!newGame" 
        @settings-result="getSettings"
      ></settings>
      <start 
        v-if="!newGame" 
        @startGame="startGame"
      ></start>
      <board 
        v-if="newGame" 
        :boardSize="boardSize"
        :playerCount="playerCount"
        :diceCount="diceCount"
      ></board>
    </div>
  </div>
</template>

<script>
import Start from '@/components/Start'
import Board from '@/components/Board'
import Settings from '@/components/Settings'

export default {
  name: 'app',
  data() {
    return {
      newGame: false,
      boardSize: 10,
      playerCount: 1,
      diceCount: 1
    }
  },
  methods: {
    startGame () {
      this.newGame = true;
    },
    finGame () {
      this.newGame = false;
    },
    getSettings (arr) {
      this.boardSize = arr[0];
      this.playerCount = arr[1];
      this.diceCount = arr[2];
    }
  },
  components: {
    Start,
    Board,
    Settings
  }
}
</script>

<style lang="less">
@import 'assets/less/style';
</style>
