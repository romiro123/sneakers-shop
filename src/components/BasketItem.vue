<script setup>
const emit = defineEmits(['onClickRemove'])

defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,

  // isFavorite: Boolean,
  // isAdded: Boolean,
  // onClickFavorite: Function,
  // onClickAdd: Function

})
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img class="w-16 h-auto" :src="imageUrl" :alt="title" />

    <div class="flex flex-col w-full">
      <p>{{ title }}</p>

      <div class="flex justify-between mt-2">
        <b>{{ price }} руб.</b>
        <img class="opacity-40 hover:opacity-100 cursor-pointer transition" src="/close.svg" alt="close"
          @click="emit('onClickRemove')" />
      </div>
    </div>
  </div>
</template>
