<script setup>
import { reactive, watch, ref, onMounted } from 'vue'
import axios from 'axios'
import { inject } from 'vue'
import CardList from '../components/CardList.vue'

const { cart, addToCart, removeFromCart } = inject('cart')

// ref - используют при работе с массивом
//state который храник все товары
const items = ref([])

//reactive - используют при работе с объектом
//state который хранит фильтры
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

//клик по + (добаление/удаление из корзины)
const onClickAddPlus = (item) => {
  console.log(item)
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
  console.log(cart)
}

//следит за изменением селекта
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

//следит за изменением поиска
const onChangeSearch = (event) => {
  filters.searchQuery = event.target.value
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
  //получаем данные из localStorage
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})



watch(filters, fetchItems)
</script>


<template>
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

  <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"></CardList>
</template>