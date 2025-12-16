<script setup lang="ts">
import { ref } from 'vue'
import CodeGenerator from '@/components/CodeGenerator.vue'
import GeneralInputs from '@/components/GeneralInputs.vue'
import NameConfigurator from '@/components/NameConfigurator.vue'
import AppHeader from "@/components/AppHeader.vue";
import RenderPreview from "@/components/RenderPreview.vue";

const userName = ref('@m00nyONE')
const customName = ref('m00ny')

const enableName = ref(true)
const enableStatic = ref(true)
const enableAnimated = ref(true)

const customNameColored = ref('|cFF5733m00ny|r')
</script>

<template>
  <AppHeader headerTitle="HodorReflexes - Configurator" />

  <div class="main-layout">
    <section class="configurator-panel">
      <GeneralInputs v-model:userName="userName" v-model:customName="customName" />

      <NameConfigurator
        :user-name="userName"
        :custom-name="customName"
        v-model="customNameColored"
      />
    </section>

    <section class="preview-panel">
      <RenderPreview :user-name="userName"
                     :enable-name="enableName"
                     :custom-name="customName"
                     :custom-name-colored="customNameColored"
                     :enable-static="enableStatic"
                     :enable-animated="enableAnimated"
      />

      <CodeGenerator
        :user-name="userName"
        :enable-name="enableName"
        :custom-name="customName"
        :custom-name-colored="customNameColored"
        :enable-static="enableStatic"
        :enable-animated="enableAnimated"
        :animationWidth=0
        :animationHeight=0
        :animationFPS=0
      />
    </section>
  </div>
</template>

<style scoped>
.main-layout {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.configurator-panel {
  flex: 1 1 0;
  max-width: 500px;
}

.preview-panel {
  flex: 1 1 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}

@media (min-width: 900px) {
  .main-layout {
    flex-direction: row;
    align-items: flex-start;
    gap: 3rem;
  }
  .configurator-panel {
    max-width: 420px;
  }
  .preview-panel {
    min-width: 0;
    flex: 1 1 0;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 1.25rem;
  }
}
</style>
