<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'

const props = defineProps<{
  userName?: string
  customName?: string
  userLabel?: string
  customLabel?: string
}>()

const emit = defineEmits<{
  (e: 'update:userName', value: string): void
  (e: 'update:customName', value: string): void
}>()

const userName = ref(props.userName ?? '')
const customName = ref(props.customName ?? '')

function undecorateDisplayName(name: string): string {
  return name.replace(/^@/, '')
}

// Prefill from URL query parameter if available and no explicit prop provided
onMounted(() => {
  if (!props.userName) {
    const params = new URLSearchParams(window.location.search)
    const qUser = params.get('userName')
    if (qUser) userName.value = qUser
    if (qUser) customName.value = undecorateDisplayName(qUser)
  }
})

watch(userName, (val) => emit('update:userName', val))
watch(customName, (val) => emit('update:customName', val))
</script>

<template>
  <div class="inputs">
    <label class="field">
      <span class="label">{{ userLabel ?? 'Username' }}</span>
      <input
        class="input"
        type="text"
        v-model="userName"
        placeholder="@m00nyONE"
        autocomplete="off"
      />
    </label>

    <label class="field">
      <span class="label">{{ customLabel ?? 'Custom Name' }}</span>
      <input
        class="input"
        type="text"
        v-model="customName"
        placeholder="m00ny"
        autocomplete="off"
      />
    </label>
  </div>
</template>

<style scoped>
.inputs {
  display: grid;
  gap: 10px;
  grid-template-columns: 1fr 1fr;
  align-items: end;
}
@media (max-width: 640px) {
  .inputs {
    grid-template-columns: 1fr;
  }
}
.field {
  display: grid;
  gap: 6px;
}
.label {
  font-size: 0.9rem;
  color: #334155; /* slate-700 */
}
.input {
  border: 1px solid #cbd5e1; /* slate-300 */
  border-radius: 6px;
  padding: 8px 10px;
  font-size: 0.95rem;
}
.input:focus {
  outline: none;
  border-color: #3b82f6; /* blue-500 */
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}
</style>
