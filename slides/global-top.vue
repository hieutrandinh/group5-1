<script setup lang="ts">
import { computed } from 'vue'
import { useNav } from '@slidev/client'

const { slides, total, currentSlideNo } = useNav()

// Footer: phần đang trình bày + số trang + thanh tiến độ.
// Slide đầu mỗi phần khai báo `part: <số>` trong frontmatter; các slide sau kế thừa cho tới phần mới.
const PARTS: Record<number, { name: string, who: string, color: string }> = {
  1: { name: 'Vì sao cần LSTM?', who: 'Nguyễn Phú Đại', color: '#ea580c' },
  2: { name: 'Bên trong một ô LSTM', who: 'Trần Vũ Tiến Minh', color: '#16a34a' },
  3: { name: 'Vì sao LSTM nhớ lâu + thí nghiệm', who: 'Trần Đình Hiếu', color: '#2563eb' },
  4: { name: 'LSTM trong thực tế', who: 'Phạm Thanh An', color: '#7c3aed' },
}
const partOf = computed(() => {
  let p = 0
  for (const s of slides.value.slice(0, currentSlideNo.value)) {
    const fm = s.meta?.slide?.frontmatter as any
    if (fm?.part !== undefined) p = Number(fm.part)
  }
  return p
})
const part = computed(() => PARTS[partOf.value])
const showFooter = computed(() => currentSlideNo.value > 1 && currentSlideNo.value < total.value)
const progress = computed(() => `${(currentSlideNo.value / total.value) * 100}%`)
</script>

<template>
  <footer v-if="showFooter" class="deck-footer" :style="{ '--part': part?.color || '#64748b' }">
    <div v-if="part" class="part">
      <span class="dot" />
      <b>Phần {{ partOf }}</b> · {{ part.name }}
      <span class="who">{{ part.who }}</span>
    </div>
    <div class="no">{{ currentSlideNo }} <span>/ {{ total }}</span></div>
    <div class="bar"><div :style="{ width: progress }" /></div>
  </footer>
</template>

<style scoped>
.deck-footer {
  position: absolute; left: 0; right: 0; bottom: 0; height: 26px;
  display: flex; align-items: center; justify-content: space-between;
  padding: 0 1.4rem 4px; font-size: 10.5px; color: #64748b; pointer-events: none;
}
.part { display: flex; align-items: center; gap: .35rem; }
.part b { color: var(--part); font-weight: 700; }
.dot { width: 7px; height: 7px; border-radius: 50%; background: var(--part); }
.who { margin-left: .4rem; padding: 0 .45rem; border-radius: 999px; background: #f1f5f9; color: #475569; }
.no { font-weight: 700; color: #334155; font-variant-numeric: tabular-nums; }
.no span { font-weight: 400; color: #94a3b8; }
.bar { position: absolute; left: 0; right: 0; bottom: 0; height: 3px; background: #e2e8f0; }
.bar div { height: 100%; background: var(--part); transition: width .4s; }
</style>
