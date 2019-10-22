<template>
  <div class="all">
    <h2 class="title">开心消消乐</h2>
    <div style="margin-bottom:20px">
      <span>得分: <b>{{score}}</b> 分</span>
      <button class="restart" @click="init">重新开始</button>
    </div>
    <div
      v-for="(item, index) in squareData"
      :key="index"
      class="row">
      <div
        v-for="(_item, _index) in item"
        :key="_index"
        class="square"
        :class="_item"
        @mousedown="dragStart(index, _index)"
        @mouseup="dragEnd">
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
      squareData: [], // 消消乐数据
      score: 10, // 得分
      startCoordinates: [0, 0], // 开始的坐标位置
      endCoordinates: [0, 0], // 结束的坐标位置
      clickSquare: [], // 点击的方块位置
    };
  },
  methods: {
    init(): void {
      this.squareData = [];
      const chars: string[] = ['A', 'S', 'D', 'F', 'G', 'H', 'J'];
      for (let i = 0; i < 10; i++) {
        for (let j = 0; j < 10; j++) {
          if (j === 0) {
            // @ts-ignore
            this.squareData.push([chars[Math.floor(Math.random() * 7)]]);
          } else {
            this.$set(this.squareData[i], j, chars[Math.floor(Math.random() * 7)]);
          }
        }
      }
      this.clear();
    },
    clear(): void {
      const m: number = 10;
      const n: number = 10;
      while (true) {
        const del: any[] = [];
        for (let i: number = 0; i < m; i++) {
          for (let j: number = 0; j < n; j++) {
            if (this.squareData[i][j] === '0') {
              continue;
            }
            let x0: number = i;
            let x1: number = i;
            let y0: number = j;
            let y1: number = j;
            while (x0 >= 0 && x0 > i - 3 && this.squareData[x0][j] === this.squareData[i][j]) {
              --x0;
            }
            while (x1 < m && x1 < i + 3 && this.squareData[x1][j] === this.squareData[i][j]) {
              ++x1;
            }
            while (y0 >= 0 && y0 > j - 3 && this.squareData[i][y0] === this.squareData[i][j]) {
              --y0;
            }
            while (y1 < n && y1 < j + 3 && this.squareData[i][y1] === this.squareData[i][j]) {
              ++y1;
            }
            if (x1 - x0 > 3 || y1 - y0 > 3) {
              del.push([i, j]);
            }
          }
        }
        if (del.length === 0) {
          break;
        }
        this.score += del.length;
        for (const square of del) {
          this.$set(this.squareData[square[0]], square[1], '0');
        }
        for (let j: number = 0; j < n; ++j) {
          let t: number = m - 1;
          for (let i: number = m - 1; i >= 0; --i) {
            if (this.squareData[i][j] !== '0') {
              [this.squareData[t][j], this.squareData[i][j]] = [this.squareData[i][j], this.squareData[t][j]];
              t -= 1;
            }
          }
        }
      }
      this.fillSquare();
    },
    dragStart(row: number, col: number): void {
      // console.log(event.offsetX, event.offsetY);
      const event = window.event || arguments[0];
      this.startCoordinates = [event.clientX, event.clientY];
      this.$set(this.clickSquare, 0, row);
      this.$set(this.clickSquare, 1, col);
    },
    dragEnd(event: any): void {
      // console.log(event.offsetX, event.offsetY);
      this.endCoordinates = [event.clientX, event.clientY];
      const x: number = this.endCoordinates[0] - this.startCoordinates[0];
      const y: number = this.endCoordinates[1] - this.startCoordinates[1];
      // console.log(this.startCoordinates, this.endCoordinates, x, y);
      const row: number = this.clickSquare[0];
      const col: number = this.clickSquare[1];
      if (Math.abs(x) >= Math.abs(y)) {
        if (x < 0 && col > 0) {
          const a: string = this.squareData[row][col];
          const b: string = this.squareData[row][col - 1];
          this.$set(this.squareData[row], col, b);
          this.$set(this.squareData[row], col - 1, a);
        } else if (x > 0 && col < 9) {
          const a: string = this.squareData[row][col];
          const b: string = this.squareData[row][col + 1];
          this.$set(this.squareData[row], col, b);
          this.$set(this.squareData[row], col + 1, a);
        }
      } else {
        if (y > 0 && row < 9) {
          const a: string = this.squareData[row][col];
          const b: string = this.squareData[row + 1][col];
          this.$set(this.squareData[row], col, b);
          this.$set(this.squareData[row + 1], col, a);
        } else if (y < 0 && row > 0) {
          const a: string = this.squareData[row][col];
          const b: string = this.squareData[row - 1][col];
          this.$set(this.squareData[row], col, b);
          this.$set(this.squareData[row - 1], col, a);
        }
      }
      this.score -= 1;
      this.clear();
    },
    fillSquare(): void {
      const chars: string[] = ['A', 'S', 'D', 'F', 'G', 'H', 'J'];
      for (let i = 0; i < 10; i++) {
        for (let j = 0; j < 10; j++) {
          if (this.squareData[i][j] === '0') {
            this.$set(this.squareData[i], j, chars[Math.floor(Math.random() * 7)]);
          }
        }
      }
    },
  },
  mounted() {
    this.init();
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
.square:active{
  animation-name: jitter;
  animation-duration: 0.5s;
  animation-iteration-count: infinite;
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
.square.H{
  background-color: rgb(142, 224, 167);
}
.square.J{
  background-color: rgb(142, 180, 224);
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

@keyframes jitter {
  from, 50%, to {
    transform: rotate(0deg);
  }
  10%, 30% {
    transform: rotate(10deg);
  }
  20% {
    transform: rotate(20deg);
  }
  60%, 80% {
    transform: rotate(-10deg);
  }
  70% {
    transform: rotate(-20deg);
  }
}
</style>