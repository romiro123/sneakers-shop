<script setup>
const props = defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})
</script>

<template>
  <div
    class="relative flex flex-col border bg-white border-slate-100 rounded-xl p-4 sm:p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition">
    <img class="absolute top-8 left-8" :src="isFavorite ? '/like-2.svg' : '/like-1.svg'" alt="like"
      @click="onClickFavorite" />
    <div class="h-full sm:h72 mb-2 flex justify-center">
      <img :src="imageUrl" alt="Sneaker" class="my-auto object-cover w-64" />
    </div>
    <p class="mb-auto">{{ title }}</p>

    <div class="flex justify-between items-center mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>

      <img :src="isAdded ? '/checked.svg' : '/plus.svg'" alt="plus" @click="onClickAdd" />
    </div>
  </div>
</template>
