<script setup>
const emit = defineEmits(['openDrawer'])
defineProps({
  totalPrice: Number
})
</script>

<template>
  <header class="px-6 sm:px-10 py-4 sm:py-8 border-b border-slate-200">
    <div class="flex justify-between
    flex-col
    md:flex-row
    md:items-center
    gap-5
    ">
      <a href="#" class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="block w-10 shrink-0" />
        <div class="">
          <h1 class="text-xl font-bold uppercase">Sneakers</h1>
          <p class="text-slate-500">Магазин кросовок</p>
        </div>
      </a>

      <ul class="flex gap-10">
        <li class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/profile.svg" alt="cart" class="shrink-0" />
          <span class="hidden sm:block">Профиль</span>
        </li>
        <li class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/heart.svg" alt="cart" class="shrink-0" />
          <span class="hidden sm:block">Закладки</span>
        </li>

        <li @click="() => emit('openDrawer')"
          class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/cart.svg" alt="cart" />
          <b>{{ totalPrice }} руб</b>
        </li>
      </ul>
    </div>
  </header>
</template>
