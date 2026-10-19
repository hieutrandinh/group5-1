<script setup lang="ts">
import { computed, ref } from 'vue'
import data from '../data/numpy_lstm.json'

// Số liệu ĐO trên mạng thật (experiment/lstm_numpy.ipynb, cài đặt chỉ bằng NumPy):
// 1 lớp RNN / LSTM, 64 nơ-ron ẩn, đầu vào 2 số, 1 nơ-ron đầu ra, bài toán cộng chuỗi 100 bước.
// reach[t] = độ nhạy của dự đoán cuối chuỗi với đầu vào ở bước t, chia cho độ nhạy ở bước cuối.
const d = data as any
const when = ref<'before' | 'after'>('after')
const k = ref(50)  // số bước đi ngược

const T = d.config.T
const W = 560, H = 230, PL = 56, PB = 34, PT = 10, PR = 10
const LOGMIN = -20, LOGMAX = 0.5
const xs = (j: number) => PL + (j / (T - 1)) * (W - PL - PR)
const ys = (v: number) => {
  const l = Math.min(LOGMAX, Math.max(LOGMIN, Math.log10(Math.max(v, 1e-300))))
  return PT + ((LOGMAX - l) / (LOGMAX - LOGMIN)) * (H - PT - PB)
}
// reach được lưu theo bước t = 1..T; đổi sang "đi ngược j bước" = phần tử T-1-j
const back = (name: string) => (j: number) => d.runs[name][when.value].reach[T - 1 - j]
const path = (name: string) => {
  let s = ''
  for (let j = 0; j < T; j++) s += `${j ? 'L' : 'M'}${xs(j).toFixed(1)},${ys(back(name)(j)).toFixed(1)}`
  return s
}
const ticks = [
  { v: 1, l: '100%' }, { v: 1e-5, l: '0.001%' }, { v: 1e-10, l: '10⁻⁸ %' }, { v: 1e-15, l: '10⁻¹³ %' }, { v: 1e-20, l: '10⁻¹⁸ %' },
]
const say = (v: number) => {
  const p = 100 * v
  if (p >= 1) return `còn ${p.toFixed(0)}%`
  if (p >= 1e-3) return `chỉ còn ${p.toPrecision(2)}%`
  return `gần như bằng 0 (${p.toExponential(0)}%)`
}
const rnnK = computed(() => back('rnn')(k.value))
const lstmK = computed(() => back('lstm')(k.value))
</script>

<template>
  <div class="sr" data-no-nav>
    <svg :viewBox="`0 0 ${W} ${H}`" class="plot">
      <g v-for="t in ticks" :key="t.v">
        <line :x1="PL" :x2="W - PR" :y1="ys(t.v)" :y2="ys(t.v)" class="grid" />
        <text :x="PL - 6" :y="ys(t.v) + 4" class="tick" text-anchor="end">{{ t.l }}</text>
      </g>
      <line :x1="xs(k)" :x2="xs(k)" :y1="PT" :y2="H - PB" class="cur" />
      <text :x="(PL + W) / 2" :y="H - 4" class="tick" text-anchor="middle">số bước đi ngược về quá khứ (0 → {{ T - 1 }})</text>
      <path :d="path('rnn')" class="rnn" />
      <path :d="path('lstm')" class="lstm" />
    </svg>
    <div class="ctrl">
      <div class="seg">
        <button :class="{ sel: when === 'before' }" @click="when = 'before'">Lúc mới khởi tạo</button>
        <button :class="{ sel: when === 'after' }" @click="when = 'after'">Sau khi học</button>
      </div>
      <label>Đi ngược <b>{{ k }}</b> bước
        <span class="sub">từ dự đoán ở bước cuối về đầu vào ở bước thứ {{ T - k }}</span>
        <input type="range" min="0" :max="T - 1" step="1" v-model.number="k"></label>
      <div class="out">
        <div>Tín hiệu lỗi về tới đó:</div>
        <div class="crnn">RNN: <b>{{ say(rnnK) }}</b></div>
        <div class="clstm">LSTM: <b>{{ say(lstmK) }}</b></div>
      </div>
      <div class="note">
        Sai số khi kiểm tra: RNN <b class="crnn">{{ d.runs.rnn.test_mse.toFixed(3) }}</b> (đoán mò ≈ 0.167),
        LSTM <b class="clstm">{{ d.runs.lstm.test_mse.toFixed(4) }}</b>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sr { display: grid; grid-template-columns: 1.6fr 1fr; gap: 1rem; align-items: center; }
.plot { width: 100%; }
.grid { stroke: #e2e8f0; }
.cur { stroke: #94a3b8; stroke-dasharray: 3 3; }
.tick { font-size: 11px; fill: #64748b; }
.rnn { fill: none; stroke: #ea580c; stroke-width: 2.5; }
.lstm { fill: none; stroke: #2563eb; stroke-width: 2.5; }
.ctrl { font-size: .8rem; }
.ctrl label { display: block; margin-bottom: .5rem; }
.ctrl .sub { display: block; font-size: .68rem; opacity: .7; color: #334155; }
.ctrl input { width: 100%; display: block; }
.seg { display: flex; gap: .4rem; margin-bottom: .6rem; }
.seg button { padding: .2rem .6rem; border-radius: 6px; border: 1px solid #94a3b8; font-size: .75rem; }
.seg button.sel { background: #2563eb; color: #fff; border-color: #2563eb; }
.out { font-size: .82rem; line-height: 1.4; }
.note { font-size: .7rem; margin-top: .5rem; opacity: .85; }
.crnn { color: #ea580c; } .clstm { color: #2563eb; }
</style>
