<template>
  <div class="min-h-screen bg-gray-50">
    <header class="sticky top-0 z-40 bg-white border-b shadow-sm">
      <div class="max-w-7xl mx-auto px-4 py-4 flex justify-between items-center">
        <h1 class="text-3xl font-bold text-orange-600">Tharusha Store</h1>

        <button
          @click="cartOpen = true"
          class="relative bg-orange-500 text-white px-5 py-2 rounded-full font-bold"
        >
          🛒 Cart
          <span class="ml-2 bg-white text-orange-600 px-2 rounded-full">
            {{ cartItemCount }}
          </span>
        </button>
      </div>
    </header>

    <section class="bg-gradient-to-r from-orange-50 via-white to-pink-50 py-12">
      <div class="max-w-5xl mx-auto px-4 text-center">
        <h2 class="text-4xl font-bold mb-6">Find products for your business</h2>

        <div class="bg-white border-2 border-orange-400 rounded-3xl p-3 flex gap-3 shadow-lg">
          <input
            v-model="searchText"
            type="text"
            placeholder="Search products..."
            class="flex-1 px-5 py-4 outline-none text-lg"
          />
          <button class="bg-orange-500 text-white px-8 py-3 rounded-2xl font-bold">
            Search
          </button>
        </div>
      </div>
    </section>

    <main class="max-w-7xl mx-auto px-4 py-8">
      <div class="flex justify-between items-center mb-6">
        <h3 class="text-3xl font-bold">Hot Picks</h3>

        <select v-model="selectedCategory" class="bg-white border rounded-xl px-4 py-3">
          <option value="">All Categories</option>
          <option v-for="category in categories" :key="category" :value="category">
            {{ category }}
          </option>
        </select>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
        <div
          v-for="product in filteredProducts"
          :key="product.id"
          class="bg-white rounded-2xl shadow hover:shadow-xl transition overflow-hidden"
        >
          <img :src="product.thumbnail" class="w-full h-48 object-cover bg-gray-100" />

          <div class="p-4">
            <p class="text-xs uppercase text-orange-600 font-bold">{{ product.category }}</p>
            <h4 class="font-bold text-lg mt-1">{{ product.title }}</h4>
            <p class="text-gray-500 text-sm line-clamp-2 mt-1">{{ product.description }}</p>

            <div class="flex justify-between items-center mt-4">
              <p class="text-2xl font-bold text-orange-600">${{ product.price }}</p>
              <p class="text-sm text-gray-500">⭐ {{ product.rating }}</p>
            </div>

            <button
              @click="addToCart(product)"
              class="mt-4 w-full bg-orange-500 hover:bg-orange-600 text-white py-3 rounded-xl font-bold"
            >
              Add to Cart
            </button>
          </div>
        </div>
      </div>
    </main>

    <!-- Attractive Cart Drawer -->
    <div v-if="cartOpen" class="fixed inset-0 z-50">
      <div
        @click="cartOpen = false"
        class="absolute inset-0 bg-black/50"
      ></div>

      <aside class="absolute right-0 top-0 h-full w-full sm:w-[430px] bg-white shadow-2xl flex flex-col">
        <div class="p-5 border-b flex justify-between items-center bg-orange-50">
          <div>
            <h2 class="text-2xl font-bold">Shopping Cart</h2>
            <p class="text-gray-500 text-sm">{{ cartItemCount }} items selected</p>
          </div>

          <button
            @click="cartOpen = false"
            class="text-3xl font-bold text-gray-500 hover:text-red-500"
          >
            ×
          </button>
        </div>

        <div class="flex-1 overflow-y-auto p-5">
          <div v-if="cart.length === 0" class="text-center mt-20">
            <div class="text-6xl mb-4">🛒</div>
            <h3 class="text-xl font-bold">Your cart is empty</h3>
            <p class="text-gray-500 mt-2">Add products to see them here.</p>
          </div>

          <div
            v-for="item in cart"
            :key="item.product.id"
            class="flex gap-4 border rounded-2xl p-3 mb-4 shadow-sm"
          >
            <img
              :src="item.product.thumbnail"
              class="w-24 h-24 object-cover rounded-xl bg-gray-100"
            />

            <div class="flex-1">
              <h4 class="font-bold line-clamp-1">{{ item.product.title }}</h4>
              <p class="text-orange-600 font-bold mt-1">${{ item.product.price }}</p>

              <div class="flex items-center gap-3 mt-3">
                <button
                  @click="decreaseQty(item.product.id)"
                  class="w-8 h-8 rounded-full bg-gray-200 font-bold"
                >
                  -
                </button>

                <span class="font-bold">{{ item.quantity }}</span>

                <button
                  @click="increaseQty(item.product.id)"
                  class="w-8 h-8 rounded-full bg-orange-500 text-white font-bold"
                >
                  +
                </button>

                <button
                  @click="removeFromCart(item.product.id)"
                  class="ml-auto text-red-500 text-sm font-bold"
                >
                  Remove
                </button>
              </div>
            </div>
          </div>
        </div>

        <div class="border-t p-5 bg-gray-50">
          <div class="flex justify-between text-lg mb-2">
            <span>Subtotal</span>
            <span class="font-bold">${{ totalPrice }}</span>
          </div>

          <div class="flex justify-between text-sm text-gray-500 mb-4">
            <span>Delivery</span>
            <span>Free</span>
          </div>

          <div class="flex justify-between text-2xl font-bold border-t pt-4">
            <span>Total</span>
            <span class="text-orange-600">${{ totalPrice }}</span>
          </div>

          <button
            class="w-full mt-5 bg-orange-500 hover:bg-orange-600 text-white py-3 rounded-xl font-bold"
          >
            Checkout
          </button>

          <button
            v-if="cart.length > 0"
            @click="clearCart"
            class="w-full mt-3 border border-red-400 text-red-500 py-3 rounded-xl font-bold"
          >
            Clear Cart
          </button>
        </div>
      </aside>
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
const cartOpen = ref(false)
const cart = ref<CartItem[]>([])

const categories = computed(() => {
  return [...new Set(products.value.map((product) => product.category))]
})

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    const matchSearch = product.title
      .toLowerCase()
      .includes(searchText.value.toLowerCase())

    const matchCategory =
      selectedCategory.value === "" || product.category === selectedCategory.value

    return matchSearch && matchCategory
  })
})

const cartItemCount = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.quantity, 0)
})

const totalPrice = computed(() => {
  return cart.value
    .reduce((sum, item) => sum + item.product.price * item.quantity, 0)
    .toFixed(2)
})

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

  const res = await fetch("https://dummyjson.com/products")
  const data: ProductResponse = await res.json()
  products.value = data.products
})
</script>