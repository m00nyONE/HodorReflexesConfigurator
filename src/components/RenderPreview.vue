<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  userName: string

  enableName?: boolean
  customName?: string
  customNameColored?: string

  enableStatic?: boolean

  enableAnimated?: boolean
}>()


const playerName = computed(() => props.customNameColored ?? props.customName ?? props.userName)

function parseString(name: string): { char: string; color: string }[] {
  const arr: { char: string; color: string }[] = []
  const regex = /\|c([0-9A-Fa-f]{6})(.*?)\|r/g
  let lastIndex = 0
  let match: RegExpExecArray | null

  if (!name.includes('|c')) {
    for (const char of name) {
      arr.push({ char, color: '#FFFFFF' })
    }
    return arr
  }

  while ((match = regex.exec(name)) !== null) {
    if (match.index > lastIndex) {
      const before = name.slice(lastIndex, match.index)
      for (const char of before) {
        arr.push({ char, color: '#FFFFFF' })
      }
    }
    const color = match[1] ? ('#' + match[1].toUpperCase()) : '#FFFFFF'
    const chars = match[2] ?? ''
    for (const char of chars) {
      arr.push({ char, color })
    }
    lastIndex = regex.lastIndex
  }
  if (lastIndex < name.length) {
    const after = name.slice(lastIndex)
    for (const char of after) {
      arr.push({ char, color: '#FFFFFF' })
    }
  }
  return arr
}
</script>

<template>
  <div id="playerRow" class="player-row" v-if="props.enableName">
    <img class="player-icon" src="@/assets/img/dragonknight.png"
      alt="Player Icon"
    />
    <div class="player-name">
      <span v-for="(item, idx) in parseString(playerName)" :key="idx" :style="{ color: item.color }">
        {{ item.char }}
      </span>
    </div>
    <div class="player-values">
      <span style="color: rgb(149, 209, 145);">123K</span><span style="color: rgb(215, 217, 150)"> (127.8K)</span>
    </div>
  </div>
</template>

<style scoped>
@font-face {
  font-family: 'Univers67';
  src: url('@/assets/fonts/Univers 67 Condensed Bold.otf') format('opentype');
  font-weight: bold;
  font-style: normal;
}
@font-face {
  font-family: 'Univers57';
  src: url('@/assets/fonts/Univers 57 Condensed Regular.otf') format('opentype');
  font-weight: normal;
  font-style: normal;
}

.player-row {
  align-items: center;
  gap: 0.25rem; /* engerer Abstand zwischen Icon und Name */
  background: rgba(0, 0, 0, 0.8);
  width: 251px;
  height: 32px;
  display: flex;
}

.player-icon {
  width: 32px;
  height: 32px;
  object-fit: contain;
}

.player-name {
  vertical-align: middle;
  font-family: 'Univers67', sans-serif;
  color: #FFFFFF;
  font-size: 1.2rem;
  font-weight: bold;
  text-shadow: 0 0 2px rgba(0, 0, 0, 1);
  flex: 1 1 auto; /* Name darf schrumpfen, um Platz für Werte zu lassen */
  min-width: 0; /* wichtig, damit Flex-Item wirklich schrumpfen darf */
  white-space: nowrap;
  overflow: hidden;
  text-overflow: clip;
}

.player-values {
  vertical-align: middle;
  font-family: 'Univers57', sans-serif;
  font-weight: normal;
  font-size: 1.1rem;
  text-align: right;
  margin-left: auto;
  flex: 0 0 auto;
  white-space: nowrap;
  min-width: 0;
}
</style>
