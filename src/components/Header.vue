<script setup>
const emit = defineEmits(['openDrawer'])
defineProps({
  totalPrice: Number
})
</script>

<template>
  <header class="px-10 py-8 border-b border-slate-200">
    <div class="flex items-center justify-between">
      <a href="#" class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="block w-10" />
        <div class="">
          <h1 class="text-xl font-bold uppercase">Sneakers</h1>
          <p class="text-slate-500">Магазин кросовок</p>
        </div>
      </a>

      <ul class="flex gap-10">
        <li @click="() => emit('openDrawer')"
          class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/cart.svg" alt="cart" />
          <b>{{ totalPrice }} руб</b>
        </li>
        <li class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/heart.svg" alt="cart" />
          <span>Закладки</span>
        </li>
        <li class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/profile.svg" alt="cart" />
          <span>Профиль</span>
        </li>
      </ul>
    </div>
  </header>
</template>
