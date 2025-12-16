<script setup lang="ts">
defineProps<{
  headerTitle?: string
}>()
</script>

<template>
  <header class="app-header">
    <img src="@/assets/img/HodorReflexes-128x128.png"
        alt="App Logo"
        width="64"
        height="64"
        class="app-logo"
    />
    <h1>{{ headerTitle }}</h1>
  </header>
</template>

<style scoped>
.app-header {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 12px; /* Abstand zwischen Logo und Text */
  padding: 18px 0 10px 0;
  background: #f1f5f9;
  border-bottom: 1px solid #e2e8f0;
  position: sticky;
  top: 0;
  z-index: 10;
}
.app-header h1 {
  margin: 0;
  font-size: 1.6rem;
  font-weight: 700;
  color: #334155;
  letter-spacing: 0.02em;
}
.app-logo {
  display: block;
}
</style>
