<template>
  <div class="all">
    <h2 class="title">开心消消乐</h2>
    <div style="margin-bottom:20px">
      <span>移动次数: <b>{{moveNum}}</b> 次</span>
      <button class="restart">重新开始</button>
    </div>
    <div v-for="(item, index) in squareData" :key="index" class="row">
      <div v-for="(_item, _index) in item" :key="_index" class="square" :class="_item">
        {{_item}}
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
export default Vue.extend({
  name: 'Home',
  data() {
    return {
      squareData: [ ['A', 'D', 'F', 'G', 'D', 'G', 'S', 'D', 'A', 'G'], ['D', 'S', 'F', 'G', 'F', 'A', 'F', 'D', 'S', 'A'],
                    ['F', 'S', 'F', 'D', 'A', 'S', 'F', 'D', 'S', 'A'], ['D', 'F', 'G', 'D', 'S', 'A', 'F', 'D', 'S', 'G'],
                    ['D', 'G', 'F', 'D', 'S', 'D', 'A', 'D', 'S', 'G'], ['S', 'F', 'D', 'A', 'F', 'S', 'D', 'S', 'A', 'F'],
                    ['F', 'S', 'F', 'D', 'A', 'S', 'F', 'D', 'S', 'A'], ['D', 'S', 'F', 'G', 'F', 'A', 'F', 'D', 'S', 'A'],
                    ['G', 'S', 'D', 'A', 'D', 'S', 'F', 'G', 'S', 'D'], ['A', 'F', 'S', 'D', 'G', 'A', 'A', 'S', 'A', 'F']],
      moveNum: 0,
    };
  },
});
</script>

<style>
.all{
  text-align: center;
  user-select: none;
}
.title{
  margin-top: 30px;
  margin-bottom: 60px;
}
.row{
  display: flex;
  line-height: 40px;
  width: 400px;
  margin: 0 auto;
  font-weight: bold;
}
.square{
  width: 40px;
  cursor: pointer;
  border-radius: 4px;
  margin: 2px;
  color: white;
}
.square.A{
  background-color: #8D98CA;
}
.square.S{
  background-color: #A9A2F6;
}
.square.D{
  background-color: #56CAD9;
}
.square.F{
  background-color: #CDBEB0;
}
.square.G{
  background-color: #D2E08E;
}

.restart{
  border: 0;
  border-radius: 14px;
  background-color: #00e2ba;
  color: white;
  padding-left: 10px;
  padding-right: 10px;
  height: 25px;
  margin-left: 10px;
  outline: none;
  cursor: pointer;
}
.restart:active{
  background-color:aquamarine;
}
</style>