<template>
  <div id="sudoku-app" class="main-wrapper">
      <h1>Sudoku Game</h1>
      <p>Welcome to the game.</p>

      <button
        @click="shuffle"
        class="button"
      >
        Create a New Game
      </button>
      <transition-group name="cell" tag="div" class="container">
        <div v-for="cell in cells" :key="cell.id" class="cell">
          {{ cell.number }}
        </div>
      </transition-group>
    </div>
</template>

<script>
import * as _ from 'lodash'

export default {
  name: 'Sudoku Game',
  data() {
    return {
      cells: Array.apply(null, { length: 81 }).map(function(_, index) {
        return {
          id: index,
          number: (index % 9) + 1
        };
      })
    };
  },
  mounted() {
    this.shuffle()
  },
  methods: {
    shuffle() {
      this.cells = _.shuffle(this.cells);
    }
  }
}
</script>

<style lang="less">
  @import "./less/global.less";
  @import "./less/cell.less";
  @import "./less/button.less";
  @import "./less/variables.less";
</style>
