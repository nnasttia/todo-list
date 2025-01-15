<template>
  <main
      class="board d-flex">
    <AppCard/>
  </main>
</template>

<script setup>
import AppCard from "@/components/AppCard.vue";

</script>

<style scoped>
  .board {
    gap: 16px;
    padding: 16px;
    justify-content: center;
    align-items: flex-start;
    flex-wrap: wrap;
  }

</style>