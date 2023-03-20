<script setup>
import {computed, reactive} from "vue";

const state = reactive({
  timer: [
    {
      time: 0,
      step: 10,
      timeout: null
    },
    {
      time: 0,
      step: 1000,
      timeout: null
    },
    {
      time: 0,
      step: 60000,
      timeout: null
    }
  ],
  results: [],
  started: false
})


const timer = computed(() => {
  const res = state.timer.map(time => time.time < 10 ? `0${time.time}` : time.time)
  return res.reverse().reduce((acc, el) => acc += `${el}:`, '').slice(0, -1)
})

const start = () => {
  state.started = true
  state.timer.forEach(item => {
    const max = state.timer.indexOf(item) === 0 ? 99 : 59
    item.timeout = setInterval(() => {
      item.time < max ? item.time++ : item.time = 0
    }, item.step)
  })
}
const stop = clear => {
  state.started = false
  state.timer.forEach(item => {
    clear && (item.time = 0)
    clearTimeout(item.timeout)
  })
}
const collect = () => {
  state.results.push(`${timer.value}`)
}
const clear = () => {
  state.results.length = 0
}
</script>

<template>
  <div class="wrapper">
    <div class="content" style="font-size: 40px">
      <div class="timer">
        <div class="timer__clock">
          {{ timer }}
        </div>
        <div class="timer__buttons">
          <button @click="state.started ? stop(false) : start()">{{ state.started ? 'stop' : 'start' }}</button>
          <button @click="()=>stop(true)">clear</button>
          <button @click="collect">collect</button>
          <button @click="clear">clear list</button>
        </div>
      </div>
      <div class="results">
        <span v-for="res in state.results">{{ res }}</span>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import "assets/main.css";
.wrapper {
  height: 100%;
}

.content {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.timer{
  width: 20rem;
  &__clock{
    font-size: 10rem;
  }
  &__buttons{
    button{
      background: none;
      border: none;
      font-size: 5rem;
      cursor: pointer;
      transition: 0.1s;
      &:hover{
        color: hotpink;
        text-decoration: underline;
        transition: 0.1s;
      }
    }
  }
}
.results{
  width: 15rem;
  max-height: 40rem;
  overflow: scroll;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  span{
    margin: 10px 20px;
  }
}
</style>
