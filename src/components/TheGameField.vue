<template>
  <div class="game-container">
    <div class="grid-container">
      <div class="cell" v-for="cell in fieldList" :style="cellStyle(cell)">
        {{ cell.value ? cell.value : '' }}
      </div>
      <div class="grid-row" v-for="row in field" :key="row">
        <div class="grid-cell" v-for="x in row" :key="row + x">
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
    const fieldList = ref(createValueList(field.value))
    window.addEventListener('keypress', moveCell)

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
    function createValueList (field) {
      let values = []
      field.forEach((row, y) => {
        row.forEach((cell, x) => {
          if (cell.value) {
            cell.x = x
            cell.y = y
            values.push(cell)
          }
        })
      })
      return values
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
        createRandomCell(field.value)
      }
      fieldList.value = createValueList(field.value)
      checkField()
    }

    function checkField () {
      for (let y = 0; y < size; y++) {
        for (let x = 0; x < size; x++ ) {
          if (!field.value[y][x].value) {
            return
          }

          if (y - 1 >= 0) {
            if (field.value[y][x].value === field.value[y - 1][x].value) {
              return
            }
          }
          if (y + 1 < size) {
            if (field.value[y][x].value === field.value[y + 1][x].value) {
              return
            }
          }
          if (x - 1 >= 0) {
            if (field.value[y][x].value === field.value[y][x - 1].value) {
              return
            }
          }
          if (x + 1 < size) {
            if (field.value[y][x].value === field.value[y][x + 1].value) {
              return
            }
          }
        }
      }
      alert('Game Over!')
    }

    function cellStyle (cell) {
      let style = `transform: translate(${cell.x * 122.5}px, ${cell.y * 122.5}px); ` + 'background-color: '
      if (cell.value === 2) {
        style += '#eee4da'
      }
      if (cell.value === 4) {
        style += '#eee1c9'
      }
      if (cell.value === 8) {
        style += '#f3b27a'
      }
      if (cell.value === 16) {
        style += '#f69664'
      }
      if (cell.value === 32) {
        style += '#f77c5f'
      }
      return style
    }

    return {
      field,
      fieldList,
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

.cell {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 55px;
  font-weight: bold;
  z-index: 2;
  width: 112.5px;
  height: 112.5px;
  border-radius: 5px;
}

.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}

@keyframes move {
  from { top: 0; left: 0; }
  to   { top: 100px; left: 100px; }
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>