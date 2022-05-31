[React 版本点这里](https://github.com/jacob-lcs/FE-BLOG/blob/master/pages/xxle.js)

> 开心消消乐-Vue版

## 构建步骤

```bash
# Project setup
yarn install

# Compiles and hot-reloads for development
yarn serve

# Compiles and minifies for production
yarn build

# Lints and fixes files
yarn lint
```

## 依赖

| Dependence | Status                                          | Description                                                  |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| Vue        | ![npm](https://img.shields.io/npm/v/vue)        | Single-page application routing                              |
| Vue-router | ![npm](https://img.shields.io/npm/v/vue-router) | Vue is a progressive framework for building user interfaces. |
| typescript | ![npm](https://img.shields.io/npm/v/typescript) | TypeScript is a language for application-scale JavaScript. |

> 之前做过一个算法题，算法要求就是写一个开心消消乐的逻辑算法，当时也是考虑了一段时间才做出来。后来想了想，既然核心算法都有了，能不能实现一个开心消消乐的小游戏呢，于是花了两天时间做了一个小游戏出来。

## 效果展示

先在这里放一个最终实现的效果，还是一个比较初级的版本，大家有什么想法欢迎评论哦

![](https://github.com/lcs1998/HappyEliminate/blob/master/assets/%E5%BC%80%E5%BF%83%E6%B6%88%E6%B6%88%E4%B9%90%E6%BC%94%E7%A4%BA.gif?raw=true)


**游戏规则：**

1. 初始时会给玩家十分的初始分，每拖动一次就减一分，每消除一个方块就加一分，直到最后分数为0游戏结束
2. 任意两个方块都可以拖动

## 界面设计

页面的布局比较简单，格子的数据是一个二维数组的形式，说到这里大家应该已经明白界面是怎么做的了。

```Vue
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
```

大家应该注意到了 `:class="_item"` 的写法，动态命名class，使得其每个种类的方块的颜色都不同，最后可以按照同色消除的玩法就行操作。

```CSS
.square.A{
  background-color: #8D98CA;
}
.square.S{
  background-color: #A9A2F6;
}
/*其余操作相同*/
```

同时在玩家点击方块的时候方块会左右摆动以表示选中了此方块，还可以提升游戏的灵动性。关于HTML动画的实现方式有很多，在这里我们使用CSS animation进行操作，代码如下：

```CSS
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
/* 只要是用户点击不动，动画就不会停止 */
.square:active{
  animation-name: jitter;
  animation-duration: 0.5s;
  animation-iteration-count: infinite;
}
```

## 核心算法

**消除算法**

上面提到我之前是做过一道题是判断一个二维数组中有没有可消的元素，有的话是多少个。

在这里我们可以这样想，最开始遍历一整个二维数组，每次定义一个 X<sub>0</sub> , X<sub>1</sub> , Y<sub>0</sub>, Y<sub>1</sub>, 然后每次计算其上下左右连续相同方块的位置，在这个过程中要注意边界问题，然后我们记录下这四个变量，只要 |X<sub>0</sub>-X<sub>1</sub>|+1>=3 或者 |Y<sub>0</sub>-Y<sub>1</sub>|+1>=3，我们就可以将这个方块的坐标加入到 `del`数组中。

遍历完一整个二维数组之后，我们就可以将 `del`数组中对应坐标位置的方块内容变为 `'0'`, 由于我们没有对 0 定义样式，所以在没有执行**下落算法**之前变为 0 的方块为白色。

**下落算法**

在我们将相应的方块白色之后，其上面的方块应该下落，在这里我的思想是这个样子的。

按照列遍历二维数组，定义一个指针 t，指向上次不为 0 的方块位置，一旦遇到方块不为 0 的格子就将其与t所指的方块就行交换，一次类推，示意图如下：

![](https://github.com/lcs1998/HappyEliminate/blob/master/assets/1571719002046.png?raw=true)

这样的话我们就可以把为空的上移到最顶层，并且不打乱顺序，然后我们在随机填充顶部的空方块就可以了。做完填充之后我们要再做一次消除算法，直到`del`数组的长度为空为止，这个道理大家应该都能想得到。

**代码如下**

```typescript
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
},
```

## 游戏结束

分数为 0 的时候游戏结束，此时在执行一遍初始化函数，重新生成一个开心消消乐格子，将分数初始化为10.

```js
if (this.score <= 0) {
    if (confirm('分数用光了哦~~')) {
      this.init();
    } else {
      this.init();
    }
  }
```
