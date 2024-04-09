<script setup>
import { computed, onMounted, provide, reactive, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

let bodyPage = document.querySelector('body')
// ref при работе с arr
//state который храник все товары
const items = ref([])

const basketItems = ref([])

//статус корзины по умолчанию
const drawerOpen = ref(false)

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

//клик по + (добаление/удаление из корзины)
const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToBasket(item)
  } else {
    removeFromBasket(item)
  }
  console.log(basketItems)
}

//добавление в массив basketItems (корзина)
const addToBasket = (item) => {
  basketItems.value.push(item)
  item.isAdded = true
}
//удаление из массива basketItems (корзина)
const removeFromBasket = (item) => {
  basketItems.value.splice(basketItems.value.indexOf(item), 1)
  item.isAdded = false
}

// //меняем на obj filters
// const sortBy = ref('')
// const searchQuery = ref('')
// const onChangeSelect = (event) => {
//   sortBy.value = event.target.value
// }
// const onChangeSearch = (event) => {
//   searchQuery.value = event.target.value
// }

//reactive при работе с obj
//state который хранит фильтры
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

//следит за изменением селекта
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

//следит за изменением поиска
const onChangeSearch = (event) => {
  filters.searchQuery = event.target.value
}

//получаем с бэка данные по favorites
const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://43f2e311d0d2dce3.mokky.dev/favorites?')
    //копия массива с проверкой
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.productId === item.id)
      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
    // console.log(items.value)
  } catch (err) {
    console.log(err)
  }
}

//отправляем или удаляем с бэка данные по favorites
const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      console.log(!item.isFavorite)
      const obj = {
        productId: item.id
      }
      item.isFavorite = true
      const { data } = await axios.post(`https://43f2e311d0d2dce3.mokky.dev/favorites`, obj)
      item.favoriteId = data.id
      // console.log(data)
    } else {
      item.isFavorite = false
      await axios.delete(`https://43f2e311d0d2dce3.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
      // console.log(data)
    }
  } catch (error) {
    console.log(error)
  }
}

//функция которая при первом рендере или при каждом изменении фильтра будет выполнять запрос на бэк
const fetchItems = async () => {
  try {
    //по умолчанию идет сортировка
    const params = {
      sortBy: filters.sortBy
    }
    //доп проверка пораметра "поиска"
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    //отправка запроса на бэк
    const { data } = await axios.get('https://43f2e311d0d2dce3.mokky.dev/item?', {
      params
    })
    //ответ с бэка записывается в items и рендерит в templat'e CardList
    items.value = data.map((obj) => ({
      ...obj,
      isAdded: false,
      isFavorite: false,
      favoriteId: null
    }))
  } catch (err) {
    console.log(err)
  }
}

//общая цена товаров в корзине
const totalPrice = computed(() => basketItems.value.reduce((acc, item) => acc + item.price, 0))

//налог 5%
const vatPrice = computed(() => Math.round((totalPrice.value / 100) * 5))

//для блокировки кнопки во время запроса отправки заказа
const isLoadCreateOrder = ref(false)

//проверка товаров в наличии
const basketIsEmpty = computed(() => basketItems.value.length === 0)

//блочим кнопку если товаров в корзине нет или если запрос отправляется
const basketButtonDisabled = computed(() => isLoadCreateOrder.value || basketIsEmpty.value)

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

//первый вариант
// onMounted(async () => {
// // через fetch
// // fetch('https://43f2e311d0d2dce3.mokky.dev/item')
// //   .then((res) => res.json())
// //   .then((data) => {
// //     console.log(data)
// // })

// // через axios
// // axios
// //   .get('https://43f2e311d0d2dce3.mokky.dev/item')
// //   .then((resp) => console.log(resp.data))

// //через try-catch
// //try {
// //  const { data } = await axios.get('https://43f2e311d0d2dce3.mokky.dev/item')
// //  items.value = data
// //} catch (e) {
// //  console.log(err)
// //}
//})

// // следит/определяет, что поменялось
// // watch(filters, async () => {
// //   try {
// //     const { data } = await axios.get(
// //       'https://43f2e311d0d2dce3.mokky.dev/item?sortBy=' + filters.sortBy
// //     )
// //     items.value = data
// //   } catch (e) {
// //     console.log(err)
// //   }
// // })

onMounted(async () => {
  const localBasketItems = localStorage.getItem('basket')

  if (localBasketItems) {
    basketItems.value = JSON.parse(localBasketItems)
  }

  console.log(basketItems.value)
  await fetchItems()
  await fetchFavorites()
})

watch(filters, fetchItems)

//следит за изменением .value корзины
watch(basketItems, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

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
  openDrawer,
  closeDrawer,
  basketItems,
  addToBasket,
  removeFromBasket
})
</script>

<template>
  <Drawer v-if="drawerOpen" :total-price="totalPrice" :vat-price="vatPrice" @create-order="createOrder"
    :button-disabled="basketButtonDisabled" />
  <div class="w-4/5 m-auto bg-white rounded-xl shadow-xl mt-10 mb-10">
    <Header @open-drawer="openDrawer" :total-price="totalPrice" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-10">Все кросовки</h2>

        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <div class="relative">
            <img src="/search.svg" alt="search" class="absolute left-3 top-3.5" />
            <input @input="onChangeSearch" type="text" placeholder="Поиск..."
              class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-grey-400" />
          </div>
        </div>
      </div>

      <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-basket="onClickAddPlus" />
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
