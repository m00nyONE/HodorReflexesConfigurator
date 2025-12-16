<script setup lang="ts">
import { computed, ref, watch } from 'vue'
import PreviewGenerator from '@/components/PreviewGenerator.vue'

const props = defineProps<{
  userName?: string
  customName?: string
  modelValue?: string // v-model:customNameColored
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()

// Local state for color selection (hex without leading #)
const colorHex = ref('FF5733')
const colorHex2 = ref('33A1FF')

// New variable for gradient mode: 'solid' | 'gradient'
const gradientMode = ref<'solid' | 'gradient'>('solid')

// Computed property for enableGradient based on gradientMode
const enableGradient = computed(() => gradientMode.value === 'gradient')

// Proxy for color inputs (#HEX <-> HEX)
const colorInput = computed({
  get: () => `#${colorHex.value}`,
  set: (val: string) => {
    const v = (val ?? '').replace(/#/g, '')
    colorHex.value = v.slice(0, 6)
  },
})
const colorInput2 = computed({
  get: () => `#${colorHex2.value}`,
  set: (val: string) => {
    const v = (val ?? '').replace(/#/g, '')
    colorHex2.value = v.slice(0, 6)
  },
})

function clampHex(hex: string): string {
  return hex.replace(/[^A-Fa-f0-9]/g, '').slice(0, 6).padEnd(6, '0').toUpperCase()
}
function hexToRgb(hex: string): [number, number, number] {
  const h = clampHex(hex)
  return [parseInt(h.slice(0, 2), 16), parseInt(h.slice(2, 4), 16), parseInt(h.slice(4, 6), 16)]
}
function rgbToHex([r, g, b]: [number, number, number]): string {
  return [r, g, b].map((v) => Math.max(0, Math.min(255, Math.round(v))).toString(16).padStart(2, '0')).join('').toUpperCase()
}

// Build ESO color-coded name: single color or gradient per char
const colored = computed(() => {
  const name = props.customName ?? ''
  const base = clampHex(colorHex.value)
  if (!name) return ''
  if (!enableGradient.value) {
    return `|c${base}${name}|r`
  }
  const alt = clampHex(colorHex2.value)
  const [r1, g1, b1] = hexToRgb(base)
  const [r2, g2, b2] = hexToRgb(alt)
  const chars = Array.from(name)
  if (chars.length === 1) {
    return `|c${base}${name}|r`
  }
  const parts: string[] = []
  for (let i = 0; i < chars.length; i++) {
    const t = i / (chars.length - 1)
    const r = r1 + (r2 - r1) * t
    const g = g1 + (g2 - g1) * t
    const b = b1 + (b2 - b1) * t
    const hx = rgbToHex([r, g, b])
    parts.push(`|c${hx}${chars[i]}|r`)
  }
  return parts.join('')
})

// Build per-character preview colors
const previewChars = computed(() => {
  const name = props.customName ?? ''
  const base = clampHex(colorHex.value)
  const chars = Array.from(name)
  if (!enableGradient.value || chars.length <= 1) {
    return chars.map((ch) => ({ ch, hex: base }))
  }
  const alt = clampHex(colorHex2.value)
  const [r1, g1, b1] = hexToRgb(base)
  const [r2, g2, b2] = hexToRgb(alt)
  return chars.map((ch, i) => {
    const t = i / (chars.length - 1)
    const r = r1 + (r2 - r1) * t
    const g = g1 + (g2 - g1) * t
    const b = b1 + (b2 - b1) * t
    const hx = rgbToHex([r, g, b])
    return { ch, hex: hx }
  })
})

// Initialize from incoming modelValue if present (one-shot)
if (props.modelValue) {
  // try to infer base hex from modelValue like |cABCDEF...
  const match = props.modelValue.match(/\|c([A-Fa-f0-9]{6})/)
  const hex = match?.[1]?.toUpperCase()
  if (hex) colorHex.value = hex
}

// Emit on change
watch(colored, (val) => emit('update:modelValue', val), { immediate: true })
</script>

<template>
  <div class="name-config">
    <div class="row">
      <div class="controls">
        <div class="field">
          <span class="lbl">Color mode</span>
          <label style="display: flex; align-items: center; gap: 4px;">
            <input type="radio" value="solid" v-model="gradientMode" />
            <span>Single</span>
          </label>
          <label style="display: flex; align-items: center; gap: 4px;">
            <input type="radio" value="gradient" v-model="gradientMode" />
            <span>Gradient</span>
          </label>
        </div>
        <label class="field">
          <span class="lbl">Farbe</span>
          <input class="input" type="color" v-model="colorInput" />
        </label>
        <label v-if="enableGradient" class="field">
          <span class="lbl">Farbe 2</span>
          <input class="input" type="color" v-model="colorInput2" />
        </label>
      </div>
    </div>
  </div>
</template>

<style scoped>
.name-config {
  display: grid;
  gap: 12px;
}
.row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}
@media (max-width: 640px) {
  .row { grid-template-columns: 1fr; }
}
.controls {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
}
.field { display: grid; gap: 6px; }
.lbl { font-size: 0.9rem; color: #334155; }
.input {
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  padding: 8px 10px;
  font-size: 0.95rem;
}
</style>
