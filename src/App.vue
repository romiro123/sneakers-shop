<script setup>
import { computed, provide, ref, watch } from 'vue'
import axios from 'axios'
import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

import HomePage from './pages/HomePage.vue';

let bodyPage = document.querySelector('body')

const items = ref([])
const basketItems = ref([])
//для блокировки кнопки во время запроса отправки заказа
const isLoadCreateOrder = ref(false)

//статус корзины по умолчанию
const drawerOpen = ref(false)

//общая цена товаров в корзине
const totalPrice = computed(() => basketItems.value.reduce((acc, item) => acc + item.price, 0))

//налог 5%
const vatPrice = computed(() => Math.round((totalPrice.value / 100) * 5))

//проверка товаров в наличии
const basketIsEmpty = computed(() => basketItems.value.length === 0)

//блочим кнопку если товаров в корзине нет или если запрос отправляется
const basketButtonDisabled = computed(() => isLoadCreateOrder.value || basketIsEmpty.value)

//закрытие корзины
const closeDrawer = () => {
  drawerOpen.value = false
  bodyPage.classList.remove('disable-scroll')
}

//открытие корзины
const openDrawer = () => {
  drawerOpen.value = true
  bodyPage.classList.add('disable-scroll')
}

//добавление в массив basketItems (корзина)
const addToBasket = (item) => {
  basketItems.value.push(item)
  item.isAdded = true

}
//удаление из массива basketItems (корзина)
const removeFromBasket = (item) => {
  basketItems.value.splice(basketItems.value.indexOf(item), 1)
  //было
  //item.isAdded = false
  /*test*/
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: basketItems.value.some((basketItem) => basketItem.id === item.id)
  }))
}

//отправляем заказ на бэк
const createOrder = async () => {
  try {
    isLoadCreateOrder.value = true
    const { data } = await axios.post(`https://43f2e311d0d2dce3.mokky.dev/orders`, {
      items: basketItems.value,
      totalPrice: totalPrice.value
    })

    basketItems.value = []

    return data
  } catch (err) {
    console.log('Error')
  } finally {
    isLoadCreateOrder.value = false
  }
}

//храним данные корзины в localStorage
watch(
  basketItems,
  () => {
    localStorage.setItem('basket', JSON.stringify(basketItems.value))
  },
  {
    deep: true
  }
)

provide('basket', {
  items,
  basketItems,
  openDrawer,
  closeDrawer,
  addToBasket,
  removeFromBasket
})


</script>

<template>
  <Drawer v-if="drawerOpen" :total-price="totalPrice" :vat-price="vatPrice" @create-order="createOrder"
    :button-disabled="basketButtonDisabled" />
  <div class=" m-auto bg-white rounded-xl shadow-xl mt-10 mb-10 
  md:w-11/12
  lg:w-4/5 
  xl:w-4/5 
  2xl:w-4/5
  
  w-11/12 ">
    <Header @open-drawer="openDrawer" :total-price="totalPrice" />

    <div class="p-6 sm:p-10">
      <HomePage></HomePage>
    </div>
  </div>
</template>

<style>
.disable-scroll {
  overflow: hidden;
  height: 100vh;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
}
</style>
