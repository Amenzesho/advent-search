<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('')
const isLoading = ref(false)
const products = ref([])

//1. call function to get the dummy API products
const findProducts = debounce(async term => {
  try {
    if (term.length === 0) {
      return (products.value = [])
    }

    isLoading.value = true

    const response = await (await fetch(`https://dummyjson.com/products/search?q=${term}&limit=5`)).json()

    products.value = response.products
  } catch (error) {
    console.error(error)
  } finally {
    isLoading.value = false
  }
})

function debounce(cb, delay = 300) {
  return (...args) => {
    setTimeout(() => {
      cb(args)
    }, delay)
  }
}
findProducts()

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input
      type="text"
      class="p-2 border-2 border-gray-dark"
      v-model="searchTerm"
      placeholder="Start typing..."
      :disabled="isLoading"
    />
    <p v-if="isLoading" class="text-lg text-center">Please wait...</p>
    <ul class="list-disc">
      <li v-for="product in products" :key="product.id">{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
