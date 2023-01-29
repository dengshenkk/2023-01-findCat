<template>
  <div class="chess" @click="handleClick" ref="chess">
    <img v-show="current === 'front'" alt="" class="front active" src="https://via.placeholder.com/150?text=Vue"
         @click="isActive = 2">
    <img class="back active" v-show="current === 'back'" alt="" :src="data.url"
         @click="isActive = 1">
  </div>
</template>

<script lang="ts">
export default {
  name: "chess"
}
</script>
<script setup lang="ts">

import {PropType, ref} from "vue";


const props = defineProps({
  data: {
    type: Object,
    required: true,
  },
  current: {
    type: String as PropType<string>,
  }
})


const chess = ref(null)

const emits = defineEmits(['click'])

const isActive = ref(1)


function handleClick(e: Event) {
  if (!e.isTrusted) {
    return alert('不可以作弊哦!')
  }
  return emits('click', props.data.data)
}



</script>
<style lang="scss" scoped>

.chess {
  width: 150px;
  height: 150px;
  border: 1px solid #333;
  margin-right:-1px;
  margin-bottom:-1px;
  overflow: hidden;

  img {
    display: none;
  }

  .active {
    display: block;
    animation: turnOver 1s;
  }

  @keyframes turnOver {
    1% {
      transform: rotateY(20deg);
    }
    100% {
      transform: rotateY(360deg);
    }
  }
}

</style>
