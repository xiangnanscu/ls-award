<script setup>
import { ref, computed, reactive } from 'vue'
import XSelect from './XSelect.vue';
import XTable from './XTable.vue';



const props = defineProps({
  records: Array
})
const ranks = reactive([
  {
    label: "一等奖",
    max: 50,
    min: 40,
    cnt: 1,
    bonus:"10000元",
    luckyGuys: []
  },
  {
    label: "二等奖",
    max: 50,
    min: 40,
    cnt: 3,
    bonus: "5000元",
    luckyGuys: []
  },
  {
    label: "三等奖",
    max: 40,
    min: 30,
    cnt: 5,
    bonus: "2000元",
    luckyGuys: []
  },
  {
    label: "四等奖",
    max: 30,
    min: 20,
    cnt: 50,
    bonus: "500元",
    luckyGuys: []
  },
  {
    label: "五等奖",
    max: 20,
    min: 10,
    cnt: 400,
    bonus: "100元",
    luckyGuys: []
  },
  {
    label: "六等奖",
    max: 10,
    min: 0,
    cnt: 1000,
    bonus: "精美礼品1份",
    luckyGuys: []
  },
])
function range(start, end) {
  return Array.from({ length: end - start + 1 }, (_, i) => i)
}
function randomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1) + min);
}
let randomIndex = ref(0)
let starteRandom = ref(false)
let currentRankIndex = ref(0)
let markRanks = computed(() => ranks.map(rank => {
  return { ...rank, records: props.records.filter(r => r['分数'] >= rank.min && r['分数'] < rank.max) }
}))
const rankIndexChoices = computed(() => ranks.map((r, i) => ({ label: r.label, value: i })))
let currentRank = computed(() => markRanks.value[currentRankIndex.value])
let currentPerson = computed(() => currentRank.value.records[randomIndex.value])
let currentPhone = computed(() => starteRandom.value ? currentPerson?.value?.手机号 : "00000000000")
let currentHintText = computed(() => {
  let cr = currentRank.value
  return `${cr.label}|${cr.cnt}/${cr.records.length}名`
})

let i = computed(() => currentPhone.value.slice(1, 2))
let j = computed(() => currentPhone.value.slice(2, 3))
let k = computed(() => currentPhone.value.slice(7, 8))
let l = computed(() => currentPhone.value.slice(8, 9))
let m = computed(() => currentPhone.value.slice(9, 10))
let n = computed(() => currentPhone.value.slice(10, 11))

let baseSize = ref(260)
let baseSpeed = ref(0.1)

let interval = ref(null)
let showSwitchButton = ref(true)
let numberHeight = computed(() => baseSize.value + 'px')
let fontHeight = computed(() => baseSize.value *0.5 + 'px')
let phoneMargin = computed(() => baseSize.value * 1.2 + 'px')
let leaveCnt = ref('0px')
let inoutCnt = ref('50px')

let downupTime = computed(() => baseSpeed.value + 's')
let inoutTime = computed(() => baseSpeed.value * 1.2 + 's')

function start(params) {
  starteRandom.value = true
  if (!interval.value) {
    interval.value = setInterval(function () {
      randomIndex.value = randomNumber(1, currentRank.value.records.length - 1)
    }, baseSpeed.value * 1000)
  }
}
function end(params) {
  starteRandom.value = false
  clearInterval(interval.value)
  interval.value = null
  const luckIndex = props.records.findIndex(e => e.身份证号 == currentPerson.value?.身份证号)
  const luckGuys = props.records.splice(luckIndex, 1)
  console.log({ luckGuys })
  currentRank.value.luckyGuys.push(...luckGuys)
}
document.addEventListener('keyup', (e) => {
  switch (e?.code) {
    case 'Space':
      e.preventDefault()
      if (!starteRandom.value) {
        start()
      } else {
        end()
      }
      break;
    case 'Digit1':
    case 'Numpad1':
      currentRankIndex.value = 0
      break
    case 'Digit2':
    case 'Numpad2':
      currentRankIndex.value = 1
      break
    case 'Digit3':
    case 'Numpad3':
      currentRankIndex.value = 2
      break
    case 'Digit4':
    case 'Numpad4':
      currentRankIndex.value = 3
      break
    case 'Digit5':
    case 'Numpad5':
      currentRankIndex.value = 4
      break
    case 'Digit6':
    case 'Numpad6':
      currentRankIndex.value = 5
      break
    default:
      break;
  }
})
</script>

<template>
  <div>
    <div class="panel">
      <div>
        <div class="hint">{{currentHintText}}</div>
        <div class="phone">
          <div class="num">
            <div>1</div>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="i">{{i}}</div>
            </transition>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="j">{{j}}</div>
            </transition>
          </div>
          <div class="num">
            <div>*</div>
          </div>
          <div class="num">
            <div>*</div>
          </div>
          <div class="num">
            <div>*</div>
          </div>
          <div class="num">
            <div>*</div>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="k">{{k}}</div>
            </transition>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="l">{{l}}</div>
            </transition>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="m">{{m}}</div>
            </transition>
          </div>
          <div class="num">
            <transition name="down-up-translate-fade">
              <div :key="n">{{n}}</div>
            </transition>
          </div>
        </div>
        <div v-for="rank in ranks">
          <h2>{{rank.label}}</h2>
          <x-table :rows="rank.luckyGuys"></x-table>
        </div>

      </div>
    </div>

  </div>


</template>

<style scoped>
.panel {
  margin-top: v-bind(phoneMargin);
}

.hint {
  font-size: v-bind(fontHeight);
  font-weight: bold;
  text-align: center;
  color:bisque;
}

.phone {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  height: v-bind(numberHeight);
  background-color: #fff;
  opacity: 0.8;
  overflow: hidden;
}

.phone .num {
  /*position: absolute;*/
  /* background: transparent; */
  color: #fff;
  opacity: 1;
  /* width: 30px; */
  height: v-bind(numberHeight);
  line-height: v-bind(numberHeight);
  font-weight: bold;
  /* font-family:'宋体'; */
  float: left;
  margin-right: 2px;
}

.phone .num div {
  background: transparent;
  color: #000;
  opacity: 1;
  /* width: 30px; */
  height: v-bind(numberHeight);
  line-height: v-bind(numberHeight);
  font-size: v-bind(numberHeight);
  font-weight: bold;
}

.in-out-translate-demo-wrapper {
  position: relative;
  height: 58px;
}

.in-out-translate-demo-wrapper button {
  position: absolute;
}

.in-out-translate-fade-enter-active,
.in-out-translate-fade-leave-active {
  transition: all v-bind(inoutTime);
  -webkit-transition: all v-bind(inoutTime);
  -moz-transition: all v-bind(inoutTime);
  -o-transition: all v-bind(inoutTime);
}

.in-out-translate-fade-enter,
.in-out-translate-fade-leave-active {
  opacity: 0;
}

.in-out-translate-fade-enter {
  transform: translateX(v-bind(inoutCnt));
  -webkit-transform: translateX(v-bind(inoutCnt));
  -moz-transform: translateX(v-bind(inoutCnt));
  -o-transform: translateX(v-bind(inoutCnt));
}

.in-out-translate-fade-leave-active {
  transform: translateX(-v-bind(inoutCnt));
  -webkit-transform: translateX(-v-bind(inoutCnt));
  -moz-transform: translateX(-v-bind(inoutCnt));
  -o-transform: translateX(-v-bind(inoutCnt));
}

.down-up-translate-fade-enter-active,
.down-up-translate-fade-leave-active {
  transition: all v-bind(downupTime);
  -webkit-transition: all v-bind(downupTime);
  -moz-transition: all v-bind(downupTime);
  -o-transition: all v-bind(downupTime);
}

.down-up-translate-fade-enter,
.down-up-translate-fade-leave-active {
  opacity: 1;
}

.down-up-translate-fade-enter {
  /*transform: translateY(40px);
            -webkit-transform: translateY(40px);
            -moz-transform: translateY(40px);
            -o-transform: translateY(40px);*/
}

.down-up-translate-fade-leave-active {
  transform: translateY(v-bind(leaveCnt));
  -webkit-transform: translateY(v-bind(leaveCnt));
  -moz-transform: translateY(v-bind(leaveCnt));
  -o-transform: translateY(v-bind(leaveCnt));
}
</style>