<template>
  <div class="game-container">
    <div class="grid-container">
      <div class="grid-row" v-for="y in 4" :key="y">
        <div class="grid-cell" v-for="x in 4" :key="y + x"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup () {
    function createField (size) {
      let field = []
      for (let y = 0; y < size; y++) {
        field.push([])
        for (let x = 1; x < size; x++) {
          field[y].push(null)
        }
      }
      return field
    }
    const field = ref(createField())

    return {
      field
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
  border-radius: 5px;
  height: 100%;
  width: calc((100% - (10px * 5)) / 4);
  margin-right: 10px;
  background-color: #767676;
}
</style>