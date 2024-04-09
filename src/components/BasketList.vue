<script setup>
import { inject } from 'vue'
import BasketItem from './BasketItem.vue'
const { basketItems, removeFromBasket } = inject('basket')
</script>

<template>
  <div class="flex flex-col gap-4 mb-auto" v-auto-animate>
    <BasketItem v-for="item in basketItems" :key="item.id" :title="item.title" :image-url="item.imageUrl"
      :price="item.price" @on-click-remove="removeFromBasket(item)" />
  </div>
</template>
