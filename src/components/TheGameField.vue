<template>
  <div class="game-container">
    <div class="grid-container">
      <div class="grid-row" v-for="row in field" :key="row">
        <div class="grid-cell" v-for="x in row" :key="row + x">
          <div class="cell-value" v-if="x.value" :style="'background-color:' + cellStyle(x.value)">{{ x.value }}</div>
        </div>
      </div>
    </div>
    <div class="cells">

    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup () {
    const size = 4
    const field = ref(createField(size))
    window.addEventListener('keydown', moveCell)

    function createField (size) {
      let field = []
      for (let y = 0; y < size; y++) {
        field.push([])
        for (let x = 0; x < size; x++) {
          field[y].push({ x: x, y: y, value: 0 })
        }
      }
      createRandomCell(field)
      createRandomCell(field)
      return field
    }

    function createRandomCell (field) {
      const emptyCells = []
      for (let y = 0; y < field.length; y++) {
        for (let x = 0; x < field[y].length; x++ ) {
          if (!field[y][x].value) {
            emptyCells.push({ y, x })
          }
        }
      }
      const cell= emptyCells[getRandomInt(0, emptyCells.length)]
      field[cell.y][cell.x].value = Math.random() < 0.6 ? 2 : 4
      return field
    }

    function getRandomInt (min, max) {
      min = Math.ceil(min)
      max = Math.floor(max)
      return Math.floor(Math.random() * (max - min)) + min
    }

    function moveCell (event) {
      if (event.key !== 'ArrowDown' && event.key !== 'ArrowUp' && event.key !== 'ArrowRight' && event.key !== 'ArrowLeft') {
        return
      }
      let newField = JSON.parse(JSON.stringify(field.value))
      let count = 0

      if (event.key === 'ArrowDown') {
        newField = newField.reverse()
      } else if (event.key === 'ArrowRight') {
        newField = newField[newField.length - 1].map((col, i) => newField.map(row => row[i])).reverse() // transpose and reverse game field
      } else if (event.key === 'ArrowLeft') {
        newField = newField[newField.length - 1].map((col, i) => newField.map(row => row[i])) // transpose game field
      }

      for (let y = 0; y < size; y++) {
        for (let x = 0; x < size; x++ ) {
          if (!newField[y][x].value) {
            for (let i = y + 1; i < size; i++) {
              if (newField[i][x].value) {
                newField[y][x].value = newField[i][x].value
                newField[i][x].value = null
                count++
                break
              }
            }
          }
          for (let i = y + 1; i < size; i++) {
            if (newField[i][x].value) {
              if (newField[i][x].value === newField[y][x].value) {
                newField[y][x].value =  newField[y][x].value + newField[i][x].value
                newField[i][x].value = null
                count++
              }
              break
            }
          }
        }
      }
      // return game field to start position
      for (let i = 0; i < size; i++) {
        for (let j = 0; j < size; j++ ) {
          field.value[newField[i][j].y][newField[i][j].x] = newField[i][j]
        }
      }
      if (count > 0) {
        createRandomCell(newField)
      }
      // checkField()
    }

    // function checkField () {
    //   let isHasEmptyCell = false
    //   let isPossibleMoves = false
    //   for (let y = 0; y < size; y++) {
    //     for (let x = 0; x < size; x++ ) {
    //       if (!field.value[y][x].value) {
    //         isHasEmptyCell = true
    //         break
    //       }
    //
    //       if (y - 1 >= 0) {
    //         isHasEmptyCell
    //       }
    //
    //     }
    //   }
    // }

    function cellStyle (value) {
      let color = 240
      if (value < 64) {
        color = 240 - value * 2
      }
      return 'rgb(255, ' + color + ', 240)'
    }

    return {
      field,
      moveCell,
      cellStyle
    }
  }
}
</script>

<style>
.game-container {
  width: 500px;
  height: 500px;
  background-color: #898989;
  border-radius: 10px;
  margin: 0 auto;
}

.grid-container {
  display: block;
  width: 100%;
  height: 100%;
  padding: 10px;
}

.grid-container :last-child {
  margin-bottom: 0;
}

.grid-row {
  display: block;
  height: calc((100% - (10px * 5)) / 4);
  margin-bottom: 10px;
}

.grid-row :last-child {
  margin-right: 0;
}

.grid-cell {
  display: inline-block;
  vertical-align: top;
  border-radius: 5px;
  height: 100%;
  width: calc((100% - (10px * 5)) / 4);
  margin-right: 10px;
  background-color: #767676;
}

.cell-value {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 55px;
  font-weight: bold;
  z-index: 2;
  width: 100%;
  height: 100%;
  border-radius: 5px;
}
</style>