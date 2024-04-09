<script setup>
defineProps({
  title: String,
  description: String,
  imageUrl: String
})
</script>


<template>
  <div class="flex flex-col items-center text-center max-w-72 m-auto">
    <img class="block w-28 h-28 mb-4" :src="imageUrl" :alt="title" />
    <h2 class="test-2xl font-medium">{{ title }}</h2>
    <p class="text-gray-400">{{ description }}</p>
  </div>
</template>


<style>
</style>