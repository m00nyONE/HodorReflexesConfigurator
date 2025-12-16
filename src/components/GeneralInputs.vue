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
const donationTier = ref(1)
const platform = ref<'PC' | 'Console'>('PC')

function undecorateDisplayName(name: string): string {
  return name.replace(/^@/, '')
}

// Prefill from URL query parameter if available and no explicit prop provided
onMounted(() => {
  const params = new URLSearchParams(window.location.search)
  const qUser = params.get('userName')
  const qCustom = params.get('customName')
  const qTier = params.get('donationTier')
  const qPlatform = params.get('platform')

  // donationTier aus Query übernehmen, falls vorhanden und gültig
  if (qTier && ['1', '2', '3'].includes(qTier)) {
    donationTier.value = Number(qTier)
  }

  // platform aus Query übernehmen, falls vorhanden und gültig
  if (qPlatform && (qPlatform === 'PC' || qPlatform === 'Console')) {
    platform.value = qPlatform
  }

  // 1. userName nicht gesetzt und customName nicht gesetzt -> nur Placeholder anzeigen
  if (!props.userName && !props.customName && !qUser && !qCustom) {
    userName.value = ''
    customName.value = ''
    return
  }

  // 2. userName gesetzt und customName nicht gesetzt -> generiere customName wie auch jetzt schon mit "escapeName"
  if (props.userName && !props.customName) {
    userName.value = props.userName
    customName.value = undecorateDisplayName(props.userName)
    return
  }

  // 3. userName gesetzt und customName gesetzt -> übernehme die Werte
  if (props.userName && props.customName) {
    userName.value = props.userName
    customName.value = props.customName
    return
  }

  // 4. userName nicht gesetzt und customName gesetzt -> zeige Placeholder für userName und übernehme customName
  if (!props.userName && props.customName) {
    userName.value = ''
    customName.value = props.customName
    return
  }

  // 5. userName nicht gesetzt und customName über URL gesetzt -> zeige Placeholder für userName und übernehme customName
  if (!props.userName && qCustom) {
    userName.value = ''
    customName.value = qCustom
    return
  }

  // 6. userName über URL gesetzt und customName nicht gesetzt -> generiere customName wie auch jetzt schon mit "escapeName"
  if (qUser && !props.customName && !qCustom) {
    userName.value = qUser
    customName.value = undecorateDisplayName(qUser)
    return
  }

  // 7. userName über URL gesetzt und customName über URL gesetzt -> beide übernehmen
  if (qUser && qCustom) {
    userName.value = qUser
    customName.value = qCustom
    return
  }
})

watch(userName, (val) => emit('update:userName', val))
watch(customName, (val) => emit('update:customName', val))
</script>

<template>
  <div class="field">
    <span class="label">Platform</span>
    <label style="display: flex; align-items: center; gap: 4px;">
      <input type="radio" value="PC" v-model="platform" />
      <span>PC</span>
    </label>
    <label style="display: flex; align-items: center; gap: 4px;">
      <input type="radio" value="Console" v-model="platform" />
      <span>Console</span>
    </label>
  </div>

  <div class="field">
    <span class="label">Donation Tier</span>
    <input type="range" min="1" max="3" v-model="donationTier" disabled style="width: 100%;" />
    <div style="display: flex; justify-content: space-between; font-size: 0.85em; color: #64748b;">
      <span>1</span><span>2</span><span>3</span>
    </div>
  </div>

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
