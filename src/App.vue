<template>
  <div id="sudoku-app" class="main-wrapper">
    <header>
      <settings-component />
    </header>
    <h1>Sudoku Game</h1>
    <template v-if="checkResults">
      <p>Congratulations, you've WON!</p>
    </template>
    <template v-else>
      <p>Welcome to the game.</p>
    </template>

    <button
      @click="init()"
      class="button"
    >
      Create a New Game
    </button>
    <transition-group name="cell" tag="div" class="container">
      <div v-for="(cell, index) in cells.flat()" :key="cell.id" class="cell">
        <template v-if="cell.hidden">
          <input
            v-model.number="cells[parseInt((index / 9), 10)][index % 9].userNumber"
            type="text"
            class="cell-input"
            @keypress="onlyNumber"
          />
        </template>
        <template v-else>
          {{ cell.number }}
        </template>
      </div>
    </transition-group>
  </div>
</template>

<script>
import * as _ from 'lodash'
import SettingsComponent from './components/Settings.vue'

export default {
  name: 'SudokuGame',
  components: {
    SettingsComponent,
  },
  data() {
    return {
      hints: parseInt(Math.random() * 5) + 35,
      cells: this.init(),
      difficultyLvls: [[35, 40], [30, 35], [25, 30], [20, 25]],
      difficulty: 0,
    };
  },
  computed: {
    checkResults() {
      const sum = (a, b) => a + b;
      for (let i = 0; i < 9; i++) {
        const row = this.getRow(this.cells, i)
        let set = new Set(row)
        if (set.size !== 9 || row.reduce(sum) !== 45) {
          return false
        }

        const column = this.getColumn(this.cells, i)
        set = new Set(column)
        if (set.size !== 9 || column.reduce(sum) !== 45) {
          return false
        }
      }
      return true
    },
  },
  mounted() {
    this.cells = this.init()
    this.$root.$on('new-game', () => {
      this.cells = this.init()
    })
    this.$root.$on('show-hint', () => {
      this.cells = this.showHint(this.cells)
    })
    this.$root.$on('select-difficulty', (lvl) => {
      this.difficulty = lvl
      this.hints = parseInt(Math.random() * 5) + 20 + ((4 - this.difficulty) * 5)
    })
    this.$root.$on('clear-user-input', () => {
      const array = this.cells = this.cells.flat().map((cell) => {
        if (cell.userNumber) {
          delete cell.userNumber;
        }
        return cell
      })
      this.cells = this.chunk(array, 9)
    })
  },
  methods: {
    // generate the base array, mix it and hide cells
    init() {
      let array =  Array.apply(null, { length: 81 }).map((_, index) => ({
          id: index,
          hidden: false,
          number: ((index % 9) + parseInt((index / 9), 10) * 3 + parseInt((index / 9 / 3), 10)) % 9 + 1,
        }))
      this.cells = array = this.hideCells(this.chunk(array, 9))
      return array
    },
    // make a two-dimentional array
    chunk(array, n) {
      return _.chunk(array, n)
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
      const temp = array[areaNum * 3 + firstRowNum] 
      array[areaNum * 3 + firstRowNum] = array[areaNum * 3 + secondRowNum] 
      array[areaNum * 3 + secondRowNum] = temp
      return array
    },
    // swap two columns for an array in the same area
    swapColumns(array) {
      return this.transpose(this.swapRows(array))
    },
    // swap two areas by rows
    swapRowsArea(array) {
      const firstAreaNum = parseInt(Math.random() * 3)
      let secondAreaNum = parseInt(Math.random() * 3)
      while (firstAreaNum === secondAreaNum) {
        secondAreaNum = parseInt(Math.random() * 3)
      }
      for (let i = 0; i < 3; i++) {
        const temp = array[firstAreaNum * 3 + i] 
        array[firstAreaNum * 3 + i] = array[secondAreaNum * 3 + i] 
        array[secondAreaNum * 3 + i] = temp
      }
      return array
    },
    // swap two areas by rows
    swapColumnsArea(array) {
      return this.transpose(this.swapRowsArea(array))
    },
    // create random sudoku field using diffrent methods
    mixArray(array, n) {
      // n it's number of shuffles in the array to get a random game
      const arrayOfMethods = [this.transpose, this.swapRows, this.swapColumns, this.swapRowsArea, this.swapColumnsArea]
      const length = arrayOfMethods.length
      for (let i = 0; i < n; i++) {
        array = arrayOfMethods[parseInt(Math.random() * length)](array)
      }
      
      return array
    },
    hideCells(array) {
      array = this.mixArray(array, 20)
      const numberOfHiddenCells = 81 - this.hints
      while (array.flat().filter((cell) => cell.hidden === true).length < numberOfHiddenCells) {
        const randomCellRow = parseInt(Math.random() * 9)
        const randomCellColumn = parseInt(Math.random() * 9) 
        array[randomCellRow][randomCellColumn].hidden = true
      }

      return array
    },
    showHint(array) {
      const flatArray = array.flat()
      const avalibleCells = flatArray.filter((cell) => (cell.hidden === true && typeof cell.userNumber === 'undefined'))
      if (avalibleCells.length > 0) {
        const randomNum = parseInt(Math.random() * (avalibleCells.length - 1))
        const randomCellNum = avalibleCells[randomNum].id

        array = flatArray.map((cell) => {
          if (cell.id === randomCellNum && cell.hidden) {
            cell.hidden = false
          }
          return cell
        })

        return this.chunk(array, 9)
      }
      return array
    },
    getRow(array, n) {
      const row = []
      for (let i = 0; i < 9; i++) {
        if (!array[n][i].hidden) {
          row.push(array[n][i].number)
        }
        if (array[n][i].userNumber) {
          row.push(parseInt(array[n][i].userNumber))
        }
      }
      return row
    },
    getColumn(array, n) {
      const column = []
      for (let i = 0; i < 9; i++) {
        if (!array[i][n].hidden) {
          column.push(array[i][n].number)
        }
        if (array[i][n].userNumber) {
          column.push(parseInt(array[i][n].userNumber))
        }
      }
      return column
    },
    getChunk(array, n) {
      return this.chunk(array, n)
    },
    onlyNumber ($event) {
      let keyCode = ($event.keyCode ? $event.keyCode : $event.which)
      // only digits from 1 to 9 are allowed
      if ((keyCode < 49 || keyCode > 57)) {
        $event.preventDefault()
      }
    },
  }
}
</script>

<style lang="less">
  @import "./less/global.less";
  @import "./less/cell.less";
  @import "./less/button.less";
</style>
