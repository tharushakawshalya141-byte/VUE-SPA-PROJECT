<template>
  <div :class="{ dark: isDarkMode }">
    <div class="min-h-screen bg-slate-50 text-slate-900 dark:bg-slate-950 dark:text-white">
      <header class="sticky top-0 z-40 bg-white/90 dark:bg-slate-900/90 backdrop-blur border-b dark:border-slate-700 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 py-4 flex items-center justify-between">
          <div>
            <h1 class="text-3xl font-black text-orange-600">Tharusha Store</h1>
            <p class="text-xs text-slate-500 dark:text-slate-400">Smart online marketplace</p>
          </div>

          <div class="flex gap-3">
            <button
              @click="toggleDarkMode"
              class="bg-slate-200 dark:bg-slate-700 px-4 py-2 rounded-full font-bold"
            >
              {{ isDarkMode ? "☀️ Light" : "🌙 Dark" }}
            </button>

            <button
              @click="cartOpen = true"
              class="bg-slate-900 dark:bg-orange-500 hover:bg-orange-600 text-white px-5 py-2 rounded-full font-bold"
            >
              🛒 Cart {{ cartItemCount }}
            </button>
          </div>
        </div>
      </header>

      <section class="bg-gradient-to-br from-orange-100 via-white to-rose-100 dark:from-slate-900 dark:via-slate-950 dark:to-orange-950">
        <div class="max-w-7xl mx-auto px-4 py-16">
          <h2 class="text-5xl font-black">
            Buy smarter with <span class="text-orange-600">Tharusha Store</span>
          </h2>

          <p class="text-slate-600 dark:text-slate-300 mt-4">
            Search products, filter categories and add items to your cart.
          </p>

          <div class="mt-8 bg-white dark:bg-slate-800 border-2 border-orange-400 rounded-3xl p-3 flex gap-3 shadow-xl">
            <input
              v-model="searchText"
              placeholder="Search products..."
              class="flex-1 px-5 py-4 outline-none rounded-2xl bg-white dark:bg-slate-800 dark:text-white"
            />

            <button class="bg-orange-500 hover:bg-orange-600 text-white px-8 rounded-2xl font-black">
              Search
            </button>
          </div>
        </div>
      </section>

      <main class="max-w-7xl mx-auto px-4 py-10">
        <div class="flex flex-col lg:flex-row gap-8">
          <aside class="lg:w-72">
            <div class="bg-white dark:bg-slate-900 rounded-3xl shadow p-5 sticky top-24">
              <h3 class="text-xl font-black mb-4">Filter Products</h3>

              <label class="text-sm font-bold text-slate-500 dark:text-slate-400">Category</label>
              <select
                v-model="selectedCategory"
                class="w-full mt-2 mb-5 bg-slate-50 dark:bg-slate-800 border dark:border-slate-700 rounded-2xl px-4 py-3 outline-none"
              >
                <option value="">All Categories</option>
                <option v-for="category in categories" :key="category" :value="category">
                  {{ category }}
                </option>
              </select>

              <label class="text-sm font-bold text-slate-500 dark:text-slate-400">Sort by Price</label>
              <select
                v-model="sortType"
                class="w-full mt-2 bg-slate-50 dark:bg-slate-800 border dark:border-slate-700 rounded-2xl px-4 py-3 outline-none"
              >
                <option value="">Default</option>
                <option value="low">Low to High</option>
                <option value="high">High to Low</option>
              </select>

              <button
                @click="resetFilters"
                class="w-full mt-5 border border-orange-400 text-orange-600 py-3 rounded-2xl font-bold"
              >
                Reset Filters
              </button>
            </div>
          </aside>

          <section class="flex-1">
            <h3 class="text-3xl font-black">Hot Picks</h3>
            <p class="text-slate-500 dark:text-slate-400 mb-6">
              {{ filteredProducts.length }} products found
            </p>

            <div class="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-3 gap-6">
              <div
                v-for="product in filteredProducts"
                :key="product.id"
                class="bg-white dark:bg-slate-900 rounded-3xl shadow hover:shadow-2xl transition overflow-hidden border dark:border-slate-700"
              >
                <div class="relative bg-slate-100 dark:bg-slate-800">
                  <img :src="product.thumbnail" class="w-full h-56 object-contain p-5" />

                  <span class="absolute top-4 left-4 bg-orange-500 text-white text-xs font-black px-3 py-1 rounded-full">
                    {{ product.discountPercentage.toFixed(0) }}% OFF
                  </span>
                </div>

                <div class="p-5">
                  <p class="text-xs uppercase text-orange-600 font-black">
                    {{ product.category }}
                  </p>

                  <h4 class="font-black text-xl mt-1 line-clamp-1">
                    {{ product.title }}
                  </h4>

                  <p class="text-sm text-slate-500 dark:text-slate-400 line-clamp-2 mt-2">
                    {{ product.description }}
                  </p>

                  <div class="flex justify-between items-center mt-4">
                    <p class="text-3xl font-black text-orange-600">${{ product.price }}</p>
                    <p class="text-sm bg-yellow-100 dark:bg-yellow-900 px-3 py-1 rounded-full font-bold">
                      ⭐ {{ product.rating }}
                    </p>
                  </div>

                  <div class="grid grid-cols-2 gap-3 mt-5">
                    <button
                      @click="selectedProduct = product"
                      class="border border-orange-500 text-orange-600 py-3 rounded-2xl font-bold"
                    >
                      View
                    </button>

                    <button
                      @click="addToCart(product)"
                      class="bg-slate-900 dark:bg-orange-500 hover:bg-orange-600 text-white py-3 rounded-2xl font-bold"
                    >
                      Add Cart
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </section>
        </div>
      </main>

      <!-- Modal -->
      <div v-if="selectedProduct" class="fixed inset-0 bg-black/60 z-50 flex items-center justify-center p-4">
        <div class="bg-white dark:bg-slate-900 max-w-4xl w-full rounded-[2rem] overflow-hidden relative shadow-2xl">
          <button
            @click="selectedProduct = null"
            class="absolute top-4 right-4 bg-white dark:bg-slate-800 shadow w-10 h-10 rounded-full text-2xl z-10"
          >
            ×
          </button>

          <div class="grid md:grid-cols-2">
            <div class="bg-slate-100 dark:bg-slate-800 p-8">
              <img :src="selectedProduct.thumbnail" class="w-full h-80 object-contain" />
            </div>

            <div class="p-8">
              <p class="text-orange-600 font-black uppercase">{{ selectedProduct.category }}</p>
              <h2 class="text-4xl font-black mt-2">{{ selectedProduct.title }}</h2>
              <p class="text-slate-600 dark:text-slate-300 mt-4">{{ selectedProduct.description }}</p>
              <p class="text-orange-600 text-5xl font-black mt-6">${{ selectedProduct.price }}</p>

              <button
                @click="addToCart(selectedProduct)"
                class="mt-8 w-full bg-orange-500 hover:bg-orange-600 text-white py-4 rounded-2xl font-black"
              >
                Add to Cart
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Cart -->
      <div v-if="cartOpen" class="fixed inset-0 z-50">
        <div @click="cartOpen = false" class="absolute inset-0 bg-black/50"></div>

        <aside class="absolute right-0 top-0 h-full w-full sm:w-[450px] bg-white dark:bg-slate-900 shadow-2xl flex flex-col">
          <div class="p-6 border-b dark:border-slate-700 bg-orange-50 dark:bg-slate-800 flex justify-between">
            <div>
              <h2 class="text-3xl font-black">Shopping Cart</h2>
              <p class="text-slate-500 dark:text-slate-400">{{ cartItemCount }} items selected</p>
            </div>

            <button @click="cartOpen = false" class="text-4xl">×</button>
          </div>

          <div class="flex-1 overflow-y-auto p-5">
            <div v-if="cart.length === 0" class="text-center mt-24">
              <div class="text-7xl">🛒</div>
              <h3 class="text-2xl font-black mt-4">Cart is empty</h3>
            </div>

            <div
              v-for="item in cart"
              :key="item.product.id"
              class="flex gap-4 bg-slate-50 dark:bg-slate-800 rounded-3xl p-4 mb-4"
            >
              <img :src="item.product.thumbnail" class="w-24 h-24 object-contain bg-white rounded-2xl" />

              <div class="flex-1">
                <h4 class="font-black line-clamp-1">{{ item.product.title }}</h4>
                <p class="text-orange-600 font-black">${{ item.product.price }}</p>

                <div class="flex items-center gap-3 mt-3">
                  <button @click="decreaseQty(item.product.id)" class="w-8 h-8 bg-white dark:bg-slate-700 rounded-full font-black">-</button>
                  <span class="font-black">{{ item.quantity }}</span>
                  <button @click="increaseQty(item.product.id)" class="w-8 h-8 bg-orange-500 text-white rounded-full font-black">+</button>
                  <button @click="removeFromCart(item.product.id)" class="ml-auto text-red-500 font-bold text-sm">
                    Remove
                  </button>
                </div>
              </div>
            </div>
          </div>

          <div class="border-t dark:border-slate-700 p-6 bg-slate-50 dark:bg-slate-800">
            <div class="flex justify-between text-2xl font-black">
              <span>Total</span>
              <span class="text-orange-600">${{ totalPrice }}</span>
            </div>

            <button class="w-full mt-5 bg-orange-500 hover:bg-orange-600 text-white py-4 rounded-2xl font-black">
              Checkout
            </button>

            <button
              v-if="cart.length > 0"
              @click="clearCart"
              class="w-full mt-3 border border-red-400 text-red-500 py-3 rounded-2xl font-black"
            >
              Clear Cart
            </button>
          </div>
        </aside>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref, watch } from "vue"
import type { Product, ProductResponse } from "./types/product"

interface CartItem {
  product: Product
  quantity: number
}

const products = ref<Product[]>([])
const searchText = ref("")
const selectedCategory = ref("")
const selectedProduct = ref<Product | null>(null)
const sortType = ref("")
const cartOpen = ref(false)
const cart = ref<CartItem[]>([])
const isDarkMode = ref(false)

const categories = computed(() => {
  return [...new Set(products.value.map((product) => product.category))]
})

const filteredProducts = computed(() => {
  let result = products.value.filter((product) => {
    const matchSearch = product.title.toLowerCase().includes(searchText.value.toLowerCase())
    const matchCategory = selectedCategory.value === "" || product.category === selectedCategory.value
    return matchSearch && matchCategory
  })

  if (sortType.value === "low") {
    result = [...result].sort((a, b) => a.price - b.price)
  }

  if (sortType.value === "high") {
    result = [...result].sort((a, b) => b.price - a.price)
  }

  return result
})

const cartItemCount = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.quantity, 0)
})

const totalPrice = computed(() => {
  return cart.value
    .reduce((sum, item) => sum + item.product.price * item.quantity, 0)
    .toFixed(2)
})

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value
  localStorage.setItem("tharusha-dark-mode", JSON.stringify(isDarkMode.value))
}

function resetFilters() {
  searchText.value = ""
  selectedCategory.value = ""
  sortType.value = ""
}

function addToCart(product: Product) {
  const existingItem = cart.value.find((item) => item.product.id === product.id)

  if (existingItem) {
    existingItem.quantity++
  } else {
    cart.value.push({ product, quantity: 1 })
  }

  cartOpen.value = true
}

function increaseQty(productId: number) {
  const item = cart.value.find((cartItem) => cartItem.product.id === productId)
  if (item) item.quantity++
}

function decreaseQty(productId: number) {
  const item = cart.value.find((cartItem) => cartItem.product.id === productId)
  if (!item) return

  if (item.quantity > 1) {
    item.quantity--
  } else {
    removeFromCart(productId)
  }
}

function removeFromCart(productId: number) {
  cart.value = cart.value.filter((item) => item.product.id !== productId)
}

function clearCart() {
  cart.value = []
}

watch(
  cart,
  () => {
    localStorage.setItem("tharusha-cart", JSON.stringify(cart.value))
  },
  { deep: true }
)

onMounted(async () => {
  const savedCart = localStorage.getItem("tharusha-cart")
  if (savedCart) {
    cart.value = JSON.parse(savedCart) as CartItem[]
  }

  const savedDarkMode = localStorage.getItem("tharusha-dark-mode")
  if (savedDarkMode) {
    isDarkMode.value = JSON.parse(savedDarkMode) as boolean
  }

  const res = await fetch("https://dummyjson.com/products")
  const data: ProductResponse = await res.json()
  products.value = data.products
})
</script>