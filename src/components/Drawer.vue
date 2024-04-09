<script setup>
import DrawerHead from './DrawerHead.vue'
import BasketList from './BasketList.vue'
import InfoBlock from './InfoBlock.vue'
// import { computed } from 'vue'

const emit = defineEmits(['createOrder'])

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
  buttonDisabled: Boolean
})
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-1/4 h-full fixed right-0 top-0 z-20">
    <div class="flex flex-col h-full overflow-auto p-8">
      <DrawerHead />

      <InfoBlock v-if="!totalPrice" title="test test" description="test test" imageUrl="/package-icon.png" />

      <BasketList v-if="totalPrice" />

      <div v-if="totalPrice" class="flex flex-col gap-3 my-5">
        <div class="flex gap-2">
          <span class="">Итого</span>
          <div class="flex-1 border-b border-dashed"></div>
          <span class="font-bold">{{ totalPrice }} руб</span>
        </div>

        <div class="flex gap-2">
          <span class="">Налог 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <span class="font-bold">{{ vatPrice }} руб</span>
        </div>
      </div>

      <button v-if="totalPrice" @click="() => emit('createOrder')" :disabled="buttonDisabled"
        class="bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition cursor-pointer">
        Оформить заказ
      </button>
    </div>
  </div>
</template>
