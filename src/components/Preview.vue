<script setup lang="ts">
import { PropType } from 'vue'

// items: Array of { ch: single character, hex: 6-digit HEX without # }
const props = defineProps({
  items: {
    type: Array as PropType<Array<{ ch: string; hex: string }>>,
    required: true,
    default: () => [],
  },
  code: {
    type: String,
    required: false,
    default: '',
  },
  label: {
    type: String,
    required: false,
    default: 'Vorschau',
  },
})
</script>

<template>
  <div class="preview">
    <label class="lbl">{{ label }}</label>
    <div class="chip">
      <span v-for="(p, idx) in items" :key="idx" :style="{ color: '#' + p.hex }">{{ p.ch }}</span>
    </div>
    <small v-if="code" class="code">{{ code }}</small>
  </div>
</template>

<style scoped>
.preview {
  display: grid;
  gap: 8px;
}
.lbl { font-size: 0.9rem; color: #334155; }
.chip {
  border: 1px dashed #94a3b8;
  border-radius: 6px;
  padding: 8px 10px;
  background: #f8fafc;
}
.code { color: #64748b; }
</style>

