<script setup lang="ts">
import { computed, ref } from 'vue'

const props = defineProps<{
  userName: string
  customName?: string
  customNameColored?: string

  enableName?: boolean
  enableStatic?: boolean
  enableAnimated?: boolean
}>()

// Default fallbacks for optional props
const enableName = computed(() => props.enableName ?? true)
const enableStatic = computed(() => props.enableStatic ?? false)
const enableAnimated = computed(() => props.enableAnimated ?? false)
const currentFolder = 'misc7' // Placeholder folder, can be made dynamic later

// Helper to escape special characters in usernames for file paths
function escapeName(name: string): string {
  return name.replace(/^@/g, '').replace(/[^\w-]/g, '')
}

// Helpers to generate individual lines
function genName(userName: string, customName?: string, customNameColored?: string): string | null {
  if (!userName || !customName || !customNameColored) return null
  return `n["${userName}"] = {"${customName}", "${customNameColored}"}`
}

function genStatic(userName: string, folder: string): string | null {
  if (!userName) return null
  return `s["${userName}"] = "LibCustomIcons/icons/${folder}/${escapeName(userName)}.dds"`
}

function genAnimated(userName: string, folder: string): string | null {
  if (!userName) return null
  return `a["${userName}"] = {"LibCustomIcons/icons/${folder}/${escapeName(userName)}_anim.dds", 0, 0, 0}`
}

const luaCode = computed(() => {
  const lines: string[] = []
  // opening code block
  lines.push('```')
  // name
  if (enableName.value) {
    const line = genName(props.userName, props.customName, props.customNameColored)
    if (line) lines.push(line)
  }
  // static
  if (enableStatic.value) {
    const line = genStatic(props.userName, currentFolder)
    if (line) lines.push(line)
  }
  // animated
  if (enableAnimated.value) {
    const line = genAnimated(props.userName, currentFolder)
    if (line) lines.push(line)
  }
  // closing code block
  lines.push('```')

  return lines.join('\n')
})

const copied = ref(false)
async function copyToClipboard() {
  try {
    await navigator.clipboard.writeText(luaCode.value)
    copied.value = true
    setTimeout(() => (copied.value = false), 1500)
  } catch (e) {
    // Fallback for browsers without clipboard API
    const textarea = document.createElement('textarea')
    textarea.value = luaCode.value
    document.body.appendChild(textarea)
    textarea.select()
    document.execCommand('copy')
    document.body.removeChild(textarea)
    copied.value = true
    setTimeout(() => (copied.value = false), 1500)
  }
}
</script>

<template>
  <div class="codegen">
    <div class="header">
      <span>Lua Code</span>
    </div>

    <div class="code-wrap">
      <div class="copy-container">
        <button
          class="copy-icon"
          @click="copyToClipboard"
          :title="copied ? 'copied to clipboard!' : 'copy to clipboard'"
          :aria-label="copied ? 'copied!' : 'copy'"
          :data-state="copied ? 'copied' : 'idle'"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" aria-hidden="true">
            <rect x="9" y="2" width="6" height="4" rx="1" ry="1" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"></rect>
            <path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"></path>
          </svg>
        </button>
        <transition name="fade">
          <span v-show="copied" class="copy-badge">Kopiert!</span>
        </transition>
      </div>
      <pre class="box"><code>{{ luaCode }}</code></pre>
    </div>

    <p class="hint">
      Aktivierte Teile: {{ enableName ? 'name ' : '' }}{{ enableStatic ? 'static ' : '' }}{{ enableAnimated ? 'animated' : '' }}
    </p>

    <transition name="fade">
      <div v-show="copied" class="toast" role="status" aria-live="polite">
        copied to clipboard!
      </div>
    </transition>
  </div>
</template>

<style scoped>
.codegen {
  display: grid;
  gap: 8px;
}
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.code-wrap {
  position: relative;
}
.copy-container {
  position: absolute;
  top: 8px;
  right: 8px;
}
.copy-icon {
  position: static;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 6px;
  border: 1px solid #33415533; /* subtle */
  background: rgba(15, 23, 42, 0.75); /* match box bg with alpha */
  color: #e2e8f0;
  cursor: pointer;
  transition: background 0.2s ease, border-color 0.2s ease, color 0.2s ease;
}
.copy-icon:hover { background: rgba(15, 23, 42, 0.9); }
.copy-icon[data-state="copied"] { color: #22c55e; border-color: #22c55e55; }
.copy-badge {
  position: absolute;
  bottom: calc(100% + 6px);
  right: 0;
  background: #22c55e; /* green-500 */
  color: #052e16; /* dark green for contrast */
  border: 1px solid #16a34a; /* green-600 */
  border-radius: 9999px;
  padding: 2px 8px;
  font-size: 12px;
  font-weight: 600;
  white-space: nowrap;
  box-shadow: 0 6px 18px rgba(0,0,0,0.25);
}
.box {
  background: #0f172a; /* slate-900 */
  color: #e2e8f0; /* slate-200 */
  padding: 12px;
  border-radius: 6px;
  white-space: pre-wrap;
  overflow: auto;
}
.hint {
  color: #64748b; /* slate-500 */
  font-size: 12px;
}
.toast {
  position: fixed;
  right: 16px;
  bottom: 16px;
  background: #0f172a; /* slate-900 */
  color: #e2e8f0; /* slate-200 */
  border: 1px solid #33415533; /* subtle */
  border-radius: 8px;
  padding: 10px 12px;
  box-shadow: 0 8px 24px rgba(0,0,0,0.3);
  z-index: 1000;
}
:deep(.fade-enter-active), :deep(.fade-leave-active) { transition: opacity .15s ease, transform .15s ease; }
:deep(.fade-enter-from), :deep(.fade-leave-to) { opacity: 0; transform: translateY(4px); }
</style>
