<template>
  <div class="container">
    <div class="header">
      <h1 class="title">2048</h1>
      <div class="control">
        <div class="score">1125</div>
        <div class="record">2048</div>
      </div>
    </div>
    <game-field></game-field>
  </div>
</template>

<script>
import TheGameField from '@/components/TheGameField.vue'

export default {
  name: 'App',
  components: {
    'game-field': TheGameField
  }
}
</script>

<style>

* {
  margin: 0;
  padding: 0;
  font-size: 18px;
  font-family: 'Oswald', sans-serif;;
}

body {
  background-color: #fffcf7;
  color: #898989;
}
.container {
  width: 500px;
  margin: 0 auto;
  margin-top: 30px;
}

.header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  margin-bottom: 30px;
}

.title {
  display: block;
  font-size: 70px;
  font-weight: bold;
}

.control {
  display: flex;
  justify-content: space-around;
  margin-top: 10px;
}

.control > div {
  display: block;
  color: white;
  font-size: 20px;
  font-weight: bold;
  height: 25px;
  margin: 10px 0 10px 10px;
  padding: 10px 25px;
  border-radius: 5px;
  background-color: #898989;
}

</style>
