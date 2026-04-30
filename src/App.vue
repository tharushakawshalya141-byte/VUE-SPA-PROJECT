<template>
  <div class="min-h-screen bg-slate-50 text-slate-900">
    <!-- Header -->
    <header class="sticky top-0 z-40 bg-white/90 backdrop-blur border-b shadow-sm">
      <div class="max-w-7xl mx-auto px-4 py-4 flex items-center justify-between">
        <div>
          <h1 class="text-3xl font-black text-orange-600">Tharusha Store</h1>
          <p class="text-xs text-slate-500">Smart online marketplace</p>
        </div>

        <nav class="hidden md:flex gap-8 font-bold">
          <span class="text-orange-600 border-b-4 border-orange-500 pb-1">Products</span>
          <span>Categories</span>
          <span>Deals</span>
          <span>About</span>
        </nav>

        <button
          @click="cartOpen = true"
          class="relative bg-slate-900 hover:bg-orange-600 text-white px-5 py-3 rounded-full font-bold transition"
        >
          🛒 Cart
          <span class="ml-2 bg-orange-500 px-2 py-0.5 rounded-full">
            {{ cartItemCount }}
          </span>
        </button>
      </div>
    </header>

    <!-- Hero -->
    <section class="relative overflow-hidden bg-gradient-to-br from-orange-100 via-white to-rose-100">
      <div class="max-w-7xl mx-auto px-4 py-16 grid lg:grid-cols-2 gap-10 items-center">
        <div>
          <p class="inline-block bg-orange-500 text-white px-4 py-2 rounded-full font-bold mb-5">
            Best deals for everyone
          </p>

          <h2 class="text-5xl md:text-6xl font-black leading-tight">
            Buy smarter with
            <span class="text-orange-600">Tharusha Store</span>
          </h2>

          <p class="text-slate-600 mt-5 text-lg">
            Search products, filter categories, view details and add items to your shopping cart.
          </p>

          <div class="mt-8 bg-white border-2 border-orange-400 rounded-3xl p-3 flex flex-col md:flex-row gap-3 shadow-xl">
            <input
              v-model="searchText"
              type="text"
              placeholder="Search products here..."
              class="flex-1 px-5 py-4 outline-none text-lg rounded-2xl"
            />

            <button class="bg-orange-500 hover:bg-orange-600 text-white px-10 py-4 rounded-2xl font-black transition">
              Search
            </button>
          </div>
        </div>

        <div class="hidden lg:block">
          <div class="bg-white rounded-[2rem] shadow-2xl p-6 rotate-2">
            <img
              src="https://cdn.dummyjson.com/product-images/laptops/apple-macbook-pro-14-inch-space-grey/thumbnail.webp"
              class="w-full h-80 object-contain"
            />
            <div class="bg-slate-900 text-white rounded-2xl p-5">
              <p class="text-orange-400 font-bold">Featured Product</p>
              <h3 class="text-2xl font-black">Premium Electronics</h3>
              <p class="text-slate-300">High quality products with amazing prices.</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Stats -->
    <section class="max-w-7xl mx-auto px-4 -mt-6 relative z-10">
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <div class="bg-white rounded-2xl shadow p-5 text-center">
          <p class="text-3xl font-black text-orange-600">{{ products.length }}</p>
          <p class="text-slate-500 font-semibold">Products</p>
        </div>
        <div class="bg-white rounded-2xl shadow p-5 text-center">
          <p class="text-3xl font-black text-orange-600">{{ categories.length }}</p>
          <p class="text-slate-500 font-semibold">Categories</p>
        </div>
        <div class="bg-white rounded-2xl shadow p-5 text-center">
          <p class="text-3xl font-black text-orange-600">{{ cartItemCount }}</p>
          <p class="text-slate-500 font-semibold">Cart Items</p>
        </div>
        <div class="bg-white rounded-2xl shadow p-5 text-center">
          <p class="text-3xl font-black text-orange-600">24/7</p>
          <p class="text-slate-500 font-semibold">Service</p>
        </div>
      </div>
    </section>

    <!-- Main -->
    <main class="max-w-7xl mx-auto px-4 py-12">
      <div class="flex flex-col lg:flex-row gap-8">
        <!-- Sidebar -->
        <aside class="lg:w-72">
          <div class="bg-white rounded-3xl shadow p-5 sticky top-24">
            <h3 class="text-xl font-black mb-4">Filter Products</h3>

            <label class="text-sm font-bold text-slate-500">Category</label>
            <select
              v-model="selectedCategory"
              class="w-full mt-2 mb-5 bg-slate-50 border rounded-2xl px-4 py-3 outline-none"
            >
              <option value="">All Categories</option>
              <option v-for="category in categories" :key="category" :value="category">
                {{ category }}
              </option>
            </select>

            <label class="text-sm font-bold text-slate-500">Sort by Price</label>
            <select
              v-model="sortType"
              class="w-full mt-2 bg-slate-50 border rounded-2xl px-4 py-3 outline-none"
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

        <!-- Products -->
        <section class="flex-1">
          <div class="flex justify-between items-center mb-6">
            <div>
              <h3 class="text-3xl font-black">Hot Picks</h3>
              <p class="text-slate-500">{{ filteredProducts.length }} products found</p>
            </div>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-3 gap-6">
            <div
              v-for="product in filteredProducts"
              :key="product.id"
              class="group bg-white rounded-3xl shadow hover:shadow-2xl transition overflow-hidden border border-slate-100"
            >
              <div class="relative bg-slate-100">
                <img
                  :src="product.thumbnail"
                  class="w-full h-56 object-contain p-5 group-hover:scale-105 transition"
                />

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

                <p class="text-sm text-slate-500 line-clamp-2 mt-2">
                  {{ product.description }}
                </p>

                <div class="flex justify-between items-center mt-4">
                  <p class="text-3xl font-black text-orange-600">${{ product.price }}</p>
                  <p class="text-sm bg-yellow-100 px-3 py-1 rounded-full font-bold">
                    ⭐ {{ product.rating }}
                  </p>
                </div>

                <div class="grid grid-cols-2 gap-3 mt-5">
                  <button
                    @click="selectedProduct = product"
                    class="border border-orange-500 text-orange-600 py-3 rounded-2xl font-bold hover:bg-orange-50"
                  >
                    View
                  </button>

                  <button
                    @click="addToCart(product)"
                    class="bg-slate-900 hover:bg-orange-600 text-white py-3 rounded-2xl font-bold transition"
                  >
                    Add Cart
                  </button>
                </div>
              </div>
            </div>
          </div>

          <p v-if="filteredProducts.length === 0" class="text-center text-slate-500 mt-16">
            No products found.
          </p>
        </section>
      </div>
    </main>

    <!-- Product Modal -->
    <div v-if="selectedProduct" class="fixed inset-0 bg-black/60 z-50 flex items-center justify-center p-4">
      <div class="bg-white max-w-4xl w-full rounded-[2rem] overflow-hidden relative shadow-2xl">
        <button
          @click="selectedProduct = null"
          class="absolute top-4 right-4 bg-white shadow w-10 h-10 rounded-full text-2xl z-10"
        >
          ×
        </button>

        <div class="grid md:grid-cols-2">
          <div class="bg-slate-100 p-8">
            <img :src="selectedProduct.thumbnail" class="w-full h-80 object-contain" />
          </div>

          <div class="p-8">
            <p class="text-orange-600 font-black uppercase">{{ selectedProduct.category }}</p>
            <h2 class="text-4xl font-black mt-2">{{ selectedProduct.title }}</h2>
            <p class="text-slate-600 mt-4">{{ selectedProduct.description }}</p>

            <div class="flex gap-3 mt-5">
              <span class="bg-yellow-100 px-4 py-2 rounded-full font-bold">⭐ {{ selectedProduct.rating }}</span>
              <span class="bg-green-100 px-4 py-2 rounded-full font-bold">Stock {{ selectedProduct.stock }}</span>
            </div>

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

    <!-- Cart Drawer -->
    <div v-if="cartOpen" class="fixed inset-0 z-50">
      <div @click="cartOpen = false" class="absolute inset-0 bg-black/50"></div>

      <aside class="absolute right-0 top-0 h-full w-full sm:w-[450px] bg-white shadow-2xl flex flex-col">
        <div class="p-6 border-b bg-orange-50 flex justify-between items-center">
          <div>
            <h2 class="text-3xl font-black">Shopping Cart</h2>
            <p class="text-slate-500">{{ cartItemCount }} items selected</p>
          </div>

          <button @click="cartOpen = false" class="text-4xl text-slate-500">×</button>
        </div>

        <div class="flex-1 overflow-y-auto p-5">
          <div v-if="cart.length === 0" class="text-center mt-24">
            <div class="text-7xl">🛒</div>
            <h3 class="text-2xl font-black mt-4">Cart is empty</h3>
            <p class="text-slate-500">Add your favorite products.</p>
          </div>

          <div
            v-for="item in cart"
            :key="item.product.id"
            class="flex gap-4 bg-slate-50 rounded-3xl p-4 mb-4"
          >
            <img :src="item.product.thumbnail" class="w-24 h-24 object-contain bg-white rounded-2xl" />

            <div class="flex-1">
              <h4 class="font-black line-clamp-1">{{ item.product.title }}</h4>
              <p class="text-orange-600 font-black">${{ item.product.price }}</p>

              <div class="flex items-center gap-3 mt-3">
                <button @click="decreaseQty(item.product.id)" class="w-8 h-8 bg-white rounded-full font-black">-</button>
                <span class="font-black">{{ item.quantity }}</span>
                <button @click="increaseQty(item.product.id)" class="w-8 h-8 bg-orange-500 text-white rounded-full font-black">+</button>

                <button @click="removeFromCart(item.product.id)" class="ml-auto text-red-500 font-bold text-sm">
                  Remove
                </button>
              </div>
            </div>
          </div>
        </div>

        <div class="border-t p-6 bg-slate-50">
          <div class="flex justify-between text-lg">
            <span>Subtotal</span>
            <span class="font-black">${{ totalPrice }}</span>
          </div>

          <div class="flex justify-between text-slate-500 mt-2">
            <span>Delivery</span>
            <span>Free</span>
          </div>

          <div class="flex justify-between text-2xl font-black border-t mt-4 pt-4">
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

  const res = await fetch("https://dummyjson.com/products")
  const data: ProductResponse = await res.json()
  products.value = data.products
})
</script>