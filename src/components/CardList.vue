<script setup>
import Card from './Card.vue'

defineProps({
  items: Array
})
const emit = defineEmits(['addToFavorite', 'addToBasket'])
</script>

<template>
  <div v-if="items.length"
    class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-3  2xl:grid-cols-4  gap-5"
    v-auto-animate>
    <Card v-for="item in items" :id="item.id" :key="item.id" :title="item.title" :image-url="item.imageUrl"
      :price="item.price" :is-added="item.isAdded" :on-click-add="() => emit('addToBasket', item)"
      :is-favorite="item.isFavorite" :on-click-favorite="() => emit('addToFavorite', item)" />
  </div>

  <span v-else>Sorry</span>
</template>
