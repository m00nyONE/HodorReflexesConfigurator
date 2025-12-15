<script setup lang="ts">
import { ref } from 'vue'
import CodeGen from '@/components/CodeGen.vue'
import Inputs from '@/components/Inputs.vue'
import NameConfigurator from '@/components/NameConfigurator.vue'

// Shared state between Inputs and CodeGen
const userName = ref('@m00nyONE')
const customName = ref('m00ny')

// Toggles can be adjusted here or wired to UI later
const enableName = ref(true)
const enableStatic = ref(true)
const enableAnimated = ref(true)

// Colored name is controlled via NameConfigurator (v-model)
const customNameColored = ref('|cFF5733m00ny|r')
</script>

<template>
  <main>
    <!-- Two-way bind Inputs to shared state -->
    <Inputs v-model:userName="userName" v-model:customName="customName" />

    <!-- Configure colored custom name; keeps in sync via v-model -->
    <NameConfigurator
      :user-name="userName"
      :custom-name="customName"
      v-model="customNameColored"
    />

    <!-- Pass current values to CodeGen so changes reflect immediately -->
    <CodeGen
      :enable-name="enableName"
      :enable-static="enableStatic"
      :enable-animated="enableAnimated"
      :user-name="userName"
      :custom-name="customName"
      :custom-name-colored="customNameColored"
    />
  </main>
</template>

<style scoped>
/* ...existing styles... */
</style>
