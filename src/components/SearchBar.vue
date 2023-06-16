<script setup lang="ts">
import { onMounted, ref } from 'vue'

const localTime = ref<string>()

interface Engine {
  icon: string
  path: string
  name: string
}

type EngineList = Array<Engine>

const searchEngine = ref<EngineList>([
  {
    icon: 'https://www.bing.com/favicon.ico',
    path: 'https://www.bing.com/search?q=',
    name: 'Bing'
  },
  {
    icon: 'https://www.google.com/favicon.ico',
    path: 'https://www.google.com/search?q=',
    name: 'Google'
  }
])
const currentEngine = ref(searchEngine.value[0])

const value = ref('')

const engineListSwitch = ref(false)

const Listener = (e: MouseEvent) => {
  if (!Array.from(document.querySelectorAll('.engine-icon ul li')).some((el) => el.contains(e.target as Node))) {
    engineListSwitch.value = false
    document.removeEventListener('mousedown', Listener)
  }
}

const showEngineList = () => {
  engineListSwitch.value = true
  document.addEventListener('mousedown', Listener)
}

const changeEngine = (e: Engine) => {
  currentEngine.value = e
  engineListSwitch.value = false
}

const error = (e: Event) => {
  if (!e.target || !(e.target instanceof HTMLImageElement)) return
  let svg = `<svg xmlns='http://www.w3.org/2000/svg' width='24' height='24'>
    <text 
      fill="skyblue"
      font-size="20"
      font-style="italic"
      font-weight="bold"
      x="4"
      y="19"
    >${e.target.alt}</text></svg>`
  svg = encodeURIComponent(svg)
  e.target.src = `data:image/svg+xml,${svg}`
}

const toSearch = () => {
  if (!value.value) return
  open(currentEngine.value.path + value.value, '_blank')
  setTimeout(() => {
    value.value = ''
  }, 10 * 1e3)
}

const waitEnter = () => {
  document.onkeydown = (e) => {
    if (e.key === 'Enter') {
      document.onkeydown = null
      toSearch()
    }
  }
}

const removeWait = () => (document.onkeydown = null)

const getTimeString = (date?: Date) => {
  let timeString = (date ?? new Date()).toLocaleTimeString()
  localTime.value = timeString.slice(0, 5)
  return getTimeString
}

onMounted(() => {
  let nowTime = new Date()
  getTimeString(nowTime)
  setTimeout(() => {
    setInterval(getTimeString(), 60 * 1e3)
  }, (60 - nowTime.getSeconds()) * 1e3)
})
</script>
<template>
  <div class="search-layout">
    <h1>CTab</h1>
    <!-- <p>CTab is a tab component.</p> -->
    <h1 class="time">{{ localTime }}</h1>
    <div class="search-box">
      <span class="engine-icon" @click="showEngineList">
        <img :src="currentEngine.icon" :alt="currentEngine.name.slice(0, 1)" @error.once="error" />
        <ul class="engine-list" :class="{ show: engineListSwitch }">
          <li v-for="engine in searchEngine" :key="engine.name" @click.stop="changeEngine(engine)">
            <img :src="engine.icon" :alt="engine.name.slice(0, 1)" srcset="" @error.once="error" />
            <span>{{ engine.name }}</span>
          </li>
        </ul>
      </span>
      <input
        class="search-input"
        type="search"
        placeholder="Search"
        v-model="value"
        @focus="waitEnter"
        @focusout="removeWait"
      />
      <span class="search-icon" @click="toSearch">
        <svg width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
          <path
            d="M10 2.75a7.25 7.25 0 015.63 11.82l4.9 4.9a.75.75 0 01-.98 1.13l-.08-.07-4.9-4.9A7.25 7.25 0 1110 2.75zm0 1.5a5.75 5.75 0 100 11.5 5.75 5.75 0 000-11.5z"
          ></path>
        </svg>
      </span>
    </div>
  </div>
</template>
<style scoped>
.search-layout {
  display: grid;
  grid-template-columns: 1fr 3fr 1fr;
  grid-template-rows: repeat(2, 1fr);
  height: 100%;
}

.time {
  grid-column: 3 / 4;
}
.search-box {
  grid-column: 2;
  grid-row: 2;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5rem;
  /* line-height: 100%; */
}

.search-box > * {
  box-sizing: border-box;
  display: inline-block;
  width: auto;
}

.search-box > span {
  position: absolute;
  left: 10px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  width: 50px;
  /* height: 100%; */
  color: skyblue;
}

.search-box > .engine-icon {
  padding-left: 5;
  padding-right: 10px;
}

.search-box > span > img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.engine-list {
  position: absolute;
  top: calc(100% + 15px);
  left: 0;
  list-style: none;
  font-size: 0.9rem;
  background-color: rgba(0, 0, 0, 0.5);
  /* border: 1px solid rgba(255, 255, 255, 0.3); */
  border-radius: 10px;
  /* box-shadow: 0 0 10px rgba(255, 255, 255, 0.5); */
  pointer-events: none;
  opacity: 0;
  transform: scale(0.1);
  transform-origin: 25px -5px;
  transition: all 0.3s ease-in-out;
}

.engine-list.show {
  pointer-events: initial;
  transform: scale(1);
  opacity: 1;
}

.engine-list::before {
  position: absolute;
  top: -5px;
  left: 15px;
  content: '';
  width: 0px;
  /* background-color: transparent; */
  border: 10px solid transparent;
  border-top: 0;
  border-bottom: 5px solid rgba(0, 0, 0, 0.5);
  /* box-shadow: 0 -5px 10px rgba(255, 255, 255, 0.5); */
  /* border-bottom: 5px solid rgba(255, 255, 255, 0.3); */
  /* border-radius: 50%; */
}

.engine-list li {
  list-style: none;
  margin: 10px;
  display: flex;
  align-items: center;
  gap: 5px;
}

.engine-list li img {
  width: 24px;
  /* font-style:oblique; */
}

.search-input {
  padding: 20px 60px;
  width: 100%;
  height: 80%;
  border: none;
  border-radius: 50px;
  font-size: inherit;
}

.search-input:focus {
  outline: 2px solid skyblue;
}

.search-box > span.search-icon {
  left: unset;
  right: 10px;
}

.search-box > span.search-icon svg {
  width: 100%;
  height: 100%;
  fill: skyblue;
}
</style>
