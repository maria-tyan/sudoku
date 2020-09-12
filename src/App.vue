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
        <div v-for="cell in cells.flat()" :key="cell.id" class="cell">
          {{ cell.number }}
        </div>
      </transition-group>

      <transition-group name="cell" tag="div" class="container">
        <div v-for="cell in swapColumns(cells).flat(2)" :key="cell.id" class="cell">
          {{ cell.number }}
        </div>
      </transition-group>

      {{ cells }}
    </div>
</template>

<script>
import * as _ from 'lodash'

export default {
  name: 'SudokuGame',
  data() {
    return {
      hints: 42,
      cells: this.chunk(this.init()),
    };
  },
  mounted() {
    this.chunk()
  },
  methods: {
    shuffle() {
      this.cells = _.shuffle(this.cells);
    },
    // generate the base array
    init() {
      return Array.apply(null, { length: 81 }).map((_, index) => ({
          id: index,
          number: ((index % 9) + parseInt((index / 9), 10) * 3) % 9 + 1,
        }))
    },
    // make a two-dimentional array
    chunk(array) {
      return _.chunk(array, 9)
    },
    // transpose an array
    transpose(array) {
      return _.zip.apply(_, array)
    },
    // swap two rows for an array in the same area
    swapRows(array) {
      const areaNum = parseInt(Math.random() * 3)
      const firstRowNum = parseInt(Math.random() * 3)
      let secondRowNum = parseInt(Math.random() * 3)
      while (firstRowNum === secondRowNum) {
        secondRowNum = parseInt(Math.random() * 3)
      }
      console.log(areaNum * 3 + firstRowNum, areaNum * 3 + secondRowNum)
      const temp = array[areaNum * 3 + firstRowNum] 
      array[areaNum * 3 + firstRowNum] = array[areaNum * 3 + secondRowNum] 
      array[areaNum * 3 + secondRowNum] = temp
      return array
    },
    // swap two columns for an array in the same area
    swapColumns(array) {
      return this.transpose(this.swapRows(array))
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
