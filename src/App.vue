<template>
  <div class="min-h-screen bg-gray-50 text-gray-900">
    <!-- Top Bar -->
    <header class="bg-white border-b sticky top-0 z-50">
      <div class="max-w-7xl mx-auto px-4 py-4 flex items-center justify-between">
        <h1 class="text-3xl font-bold text-orange-600">Tharusha Store</h1>

        <nav class="hidden md:flex gap-8 font-semibold">
          <span class="text-orange-600 border-b-4 border-orange-500">Products</span>
          <span>Categories</span>
          <span>Worldwide</span>
        </nav>

        <button
          class="bg-orange-500 text-white px-5 py-2 rounded-full font-semibold"
        >
          Cart {{ cart.length }}
        </button>
      </div>
    </header>

    <!-- Hero Search -->
    <section class="bg-gradient-to-r from-orange-50 via-white to-pink-50 py-12">
      <div class="max-w-5xl mx-auto px-4 text-center">
        <h2 class="text-4xl md:text-5xl font-bold mb-3">
          Find products for your business
        </h2>
        <p class="text-gray-500 mb-8">
          Search electronics, beauty, groceries, furniture and more
        </p>

        <div class="bg-white border-2 border-orange-400 rounded-3xl p-3 flex flex-col md:flex-row gap-3 shadow-lg">
          <input
            v-model="searchText"
            type="text"
            placeholder="Search products..."
            class="flex-1 px-5 py-4 outline-none text-lg"
          />

          <button class="bg-orange-500 hover:bg-orange-600 text-white px-10 py-4 rounded-2xl font-bold">
            Search
          </button>
        </div>
      </div>
    </section>

    <!-- Main -->
    <main class="max-w-7xl mx-auto px-4 py-8">
      <!-- Filters -->
      <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-8">
        <h3 class="text-3xl font-bold">Hot Picks</h3>

        <select
          v-model="selectedCategory"
          class="bg-white border rounded-xl px-4 py-3 shadow-sm"
        >
          <option value="">All Categories</option>
          <option
            v-for="category in categories"
            :key="category"
            :value="category"
          >
            {{ category }}
          </option>
        </select>
      </div>

      <!-- Products -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
        <div
          v-for="product in filteredProducts"
          :key="product.id"
          class="bg-white rounded-2xl shadow hover:shadow-xl transition overflow-hidden"
        >
          <img
            :src="product.thumbnail"
            class="w-full h-48 object-cover bg-gray-100"
          />

          <div class="p-4">
            <p class="text-xs uppercase text-orange-600 font-bold mb-1">
              {{ product.category }}
            </p>

            <h4 class="font-bold text-lg line-clamp-1">
              {{ product.title }}
            </h4>

            <p class="text-sm text-gray-500 line-clamp-2 mt-1">
              {{ product.description }}
            </p>

            <div class="flex items-center justify-between mt-4">
              <p class="text-2xl font-bold text-orange-600">
                ${{ product.price }}
              </p>

              <p class="text-sm text-gray-500">
                ⭐ {{ product.rating }}
              </p>
            </div>

            <div class="flex gap-2 mt-4">
              <button
                @click="selectedProduct = product"
                class="flex-1 border border-orange-500 text-orange-600 py-2 rounded-xl font-semibold"
              >
                View
              </button>

              <button
                @click="addToCart(product)"
                class="flex-1 bg-orange-500 text-white py-2 rounded-xl font-semibold"
              >
                Add Cart
              </button>
            </div>
          </div>
        </div>
      </div>

      <p
        v-if="filteredProducts.length === 0"
        class="text-center text-gray-500 mt-10"
      >
        No products found.
      </p>
    </main>

    <!-- Product Detail Modal -->
    <div
      v-if="selectedProduct"
      class="fixed inset-0 bg-black/50 flex items-center justify-center p-4 z-50"
    >
      <div class="bg-white max-w-2xl w-full rounded-3xl p-6 relative">
        <button
          @click="selectedProduct = null"
          class="absolute top-4 right-4 text-2xl"
        >
          ×
        </button>

        <img
          :src="selectedProduct.thumbnail"
          class="w-full h-64 object-cover rounded-2xl bg-gray-100"
        />

        <h2 class="text-3xl font-bold mt-4">
          {{ selectedProduct.title }}
        </h2>

        <p class="text-gray-600 mt-2">
          {{ selectedProduct.description }}
        </p>

        <p class="text-orange-600 text-3xl font-bold mt-4">
          ${{ selectedProduct.price }}
        </p>

        <button
          @click="addToCart(selectedProduct)"
          class="mt-5 w-full bg-orange-500 text-white py-3 rounded-xl font-bold"
        >
          Add to Cart
        </button>
      </div>
    </div>

    <!-- Cart Section -->
    <section class="max-w-7xl mx-auto px-4 pb-10">
      <div class="bg-white rounded-2xl shadow p-5">
        <h3 class="text-2xl font-bold mb-4">Shopping Cart</h3>

        <p v-if="cart.length === 0" class="text-gray-500">
          Cart is empty.
        </p>

        <div
          v-for="item in cart"
          :key="item.id"
          class="flex justify-between border-b py-3"
        >
          <span>{{ item.title }}</span>
          <span class="font-bold">${{ item.price }}</span>
        </div>

        <div class="text-right text-xl font-bold mt-4">
          Total: ${{ totalPrice }}
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from "vue"
import type { Product, ProductResponse } from "./types/product"

const products = ref<Product[]>([])
const searchText = ref("")
const selectedCategory = ref("")
const selectedProduct = ref<Product | null>(null)
const cart = ref<Product[]>([])

const categories = computed(() => {
  return [...new Set(products.value.map((product) => product.category))]
})

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    const matchSearch = product.title
      .toLowerCase()
      .includes(searchText.value.toLowerCase())

    const matchCategory =
      selectedCategory.value === "" ||
      product.category === selectedCategory.value

    return matchSearch && matchCategory
  })
})

const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.price, 0)
})

function addToCart(product: Product) {
  cart.value.push(product)
  localStorage.setItem("cart", JSON.stringify(cart.value))
}

onMounted(async () => {
  const savedCart = localStorage.getItem("cart")

  if (savedCart) {
    cart.value = JSON.parse(savedCart) as Product[]
  }

  const res = await fetch("https://dummyjson.com/products")
  const data: ProductResponse = await res.json()
  products.value = data.products
})
</script>