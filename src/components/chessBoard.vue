<template>
  <div class="chessBoard-wrap">
    <div class="tips"><span v-if="isWin">你赢了 <span v-if="second< 10"> 但是你作弊了吧 - - </span></span> 耗时:
      {{ second }} 秒
    </div>
    <div class="chessBoard">
      <chess v-for="(row, i) in board" :key="i" :current="row.current" :data="row" @click="handleClick(row)"/>
  </div>
  </div>
</template>

<script lang="ts">
import Chess from "./chess.vue";

export default {
  name: "chessBoard",
  components: {Chess}
}
</script>

<script setup lang="ts">
import confetti from "canvas-confetti";
import {computed, ref, watchEffect} from "vue";

import cat1 from "../assets/images/1.png";
import cat2 from "../assets/images/2.png";
import cat3 from "../assets/images/3.png";
import cat4 from "../assets/images/4.png";
import cat5 from "../assets/images/5.png";
import cat6 from "../assets/images/6.png";
import cat7 from "../assets/images/7.png";
import cat8 from "../assets/images/8.png";


type chess = {
  data: number,
  current: string,
  url: string
}
const catImages = {
  1: cat1,
  2: cat2,
  3: cat3,
  4: cat4,
  5: cat5,
  6: cat6,
  7: cat7,
  8: cat8
}

let preData: number | null = null
const FRONT = 'front'
const BACK = 'back'
let selectedArr: chess[] = []
let timer: number | null = null
let second = ref(0)

function useConfetti(count: number) {
  function randomInRange(min: number, max: number) {
    return Math.random() * (max - min) + min;
  }

  function createConfetti() {
    confetti({
      angle: randomInRange(55, 125),
      spread: randomInRange(50, 70),
      particleCount: randomInRange(10, 100),
      origin: {y: 0.5}
    })
  }

  function putConfetti() {
    while (count > 0) {
      setTimeout(() => {
        createConfetti()
      }, 100 * count)
      count--
    }
  }

  return {putConfetti}
}

function startTime() {
  timer = setInterval(() => {
    second.value++
  }, 1000)
}
function handleClick(row: chess) {
  if (!timer) {
    startTime()
  }
  // 反面不能点
  if (row.current === BACK) {
    return
  }

  // 点击就变反面
  row.current = BACK
  // 保存点击的卡片
  selectedArr.push(row)

  // 如果没有上一个就把当前的卡片当做上一个, 并return掉
  if (preData === null) {
    preData = row.data
    return
  }

  // 拿上一个和当前点击的对比,如果不一样的话, 就需要把之前和现在的翻面为正面, 并清空记录, 否则的话只需要清空记录
  if (preData !== row.data) {
    clear(selectedArr)
    selectedArr = []
    preData = null
  }else {
    preData = null
    selectedArr = []
  }
}
function clear(arr: chess[]) {
  arr.forEach(item => {
    setTimeout(() => {
      item.current = FRONT
    }, 150)
  })
}


const data: chess[] = [
  {data: 1, current: 'front', url: ''},
  {data: 1, current: 'front', url: ''},
  {data: 2, current: 'front', url: ''},
  {data: 2, current: 'front', url: ''},
  {data: 3, current: 'front', url: ''},
  {data: 3, current: 'front', url: ''},
  {data: 4, current: 'front', url: ''},
  {data: 4, current: 'front', url: ''},
  {data: 5, current: 'front', url: ''},
  {data: 5, current: 'front', url: ''},
  {data: 6, current: 'front', url: ''},
  {data: 6, current: 'front', url: ''},
  {data: 7, current: 'front', url: ''},
  {data: 7, current: 'front', url: ''},
  {data: 8, current: 'front', url: ''},
  {data: 8, current: 'front', url: ''},
].map(item => {
  // @ts-ignore
  item.url = catImages[item.data]
  return item
}).sort(() => Math.random() - 0.5)


const board = ref<chess[]>(data)

const {putConfetti} = useConfetti(10);
const isWin = computed(() => {
  return board.value.every(item => item.current === BACK)
})


watchEffect(() => {
  // 赢了以后停止定时器
  if (isWin.value) {
    putConfetti()
    clearInterval(timer as number)
  }
})
</script>
<style lang="scss" scoped>

.chessBoard-wrap, .tips {
  margin: 24px auto;
  text-align: center;
}
.chessBoard {
  margin: 0 auto;
  display: flex;
  width: 600px;
  flex-wrap: wrap;
  background-image: url("../assets/images/background.png");
  background-size: contain;
}
</style>
