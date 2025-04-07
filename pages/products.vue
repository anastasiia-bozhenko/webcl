<template>
  <div>
    <UCard>
      <!-- Статусы загрузки и ошибок -->
      <div v-if="pending">Завантаження...</div>
      <div v-else-if="error">Помилка запиту: {{ error }}</div>
      <div v-else>
        <!-- Если данных нет, показываем сообщение -->
        <div v-if="!productsList.length">Нет данных для отображения</div>
        <!-- Если данные есть, отображаем таблицу -->
        <UTable
            v-else
            :rows="productsList"
            :columns="columns"
            :sortable="true"
            :pagination="{ page: 1, pageSize: 10 }"
            :filter="{ placeholder: 'Пошук...' }"
            empty-text="Дані завантажено, але таблиця порожня."
        >
          <template #rating-data="{ row }">
            <span :class="row.rating < 4.5 ? 'text-red-500' : 'text-green-600'">
              {{ row.rating }}
            </span>
          </template>
          <template #thumbnail-data="{ row }">
            <img :src="row.thumbnail" alt="Product Image" width="100" height="100" />
          </template>
        </UTable>
      </div>
    </UCard>

    <!-- Отладочная информация -->
    <pre>{{ productsList }}</pre>
    <pre>{{ columns }}</pre>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// Создаем реактивный ref для списка продуктов
const productsList = ref([])

// Определяем колонки для таблицы
const columns = [
  { id: 'title', name: 'Назва', key: 'title', sortable: true },
  { id: 'price', name: 'Ціна', key: 'price', sortable: true },
  { id: 'rating', name: 'Рейтинг', key: 'rating', sortable: true },
  { id: 'thumbnail', name: 'Зображення', key: 'thumbnail', render: 'thumbnail-data' },
  { id: 'stock', name: 'Кількість в наявності', key: 'stock' },
  { id: 'category', name: 'Категорія', key: 'category' }
]

// Используем async/await для загрузки данных
const fetchProducts = async () => {
  try {
    const response = await fetch('https://dummyjson.com/products')
    const data = await response.json()

    console.log('Полученные данные:', data)  // Добавь это

    if (data && Array.isArray(data.products)) {
      productsList.value = data.products
      console.log('productsList обновлен:', productsList.value)
    } else {
      console.error('Данные не загружены или не найдены в products:', data)
    }
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
  }
}

onMounted(() => {
  fetchProducts()
})
</script>


