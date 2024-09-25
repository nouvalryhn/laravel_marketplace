<script setup lang="ts">
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import { ref, computed } from 'vue';

interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
  category: string;
  description: string;
}

interface CartItem {
  product: Product;
  quantity: number;
}

// Mock product data
const products = ref<Product[]>([
  { id: 1, name: 'Smartphone Samsung', price: 3499000, image: '/images/phone.png', category: 'Electronics', description: 'Latest model with advanced features.' },
  { id: 2, name: 'Nike Running Shoes', price: 2499000, image: '/images/shoes.png', category: 'Sports', description: 'Comfortable shoes for your daily run.' },
  { id: 3, name: 'Coffee Maker', price: 359000, image: '/images/coffee.png', category: 'Home', description: 'Start your day with freshly brewed coffee.' },
  { id: 4, name: 'Novel: "Negeri di Ujung Tanduk"', price: 75000, image: '/images/book.png', category: 'Books', description: 'Bestselling novel of the year.' },
  { id: 5, name: 'Wireless Headphones Sony', price: 5149000, image: '/images/headphone.png', category: 'Electronics', description: 'High-quality sound with noise cancellation.' },
]);

const cart = ref<CartItem[]>([]);
const searchQuery = ref('');
const selectedCategory = ref('All');

const categories = computed(() => ['All', ...new Set(products.value.map(p => p.category))]);

const filteredProducts = computed(() => {
  return products.value.filter(product => {
    const matchesSearch = product.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
                          product.description.toLowerCase().includes(searchQuery.value.toLowerCase());
    const matchesCategory = selectedCategory.value === 'All' || product.category === selectedCategory.value;
    return matchesSearch && matchesCategory;
  });
});

const addToCart = (product: Product) => {
  const existingItem = cart.value.find(item => item.product.id === product.id);
  if (existingItem) {
    existingItem.quantity++;
  } else {
    cart.value.push({ product, quantity: 1 });
  }
};

const removeFromCart = (index: number) => {
  cart.value.splice(index, 1);
};

const cartTotal = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.product.price * item.quantity, 0).toLocaleString('id-ID');
});

</script>

<template>
    <Head title="Marketplace" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800 dark:text-gray-200">
                Marketplace
            </h2>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg dark:bg-gray-800">
                    <div class="p-6 text-gray-900 dark:text-gray-100">
                        <div class="mb-4 flex space-x-4">
                            <input v-model="searchQuery" type="text" placeholder="Search products..." class="flex-grow p-2 border rounded dark:bg-gray-700">
                            <select v-model="selectedCategory" class="p-2 border rounded dark:bg-gray-700">
                                <option v-for="category in categories" :key="category">{{ category }}</option>
                            </select>
                        </div>

                        <h3 class="text-lg font-semibold mb-4">Products</h3>
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                            <div v-for="product in filteredProducts" :key="product.id" class="border p-4 rounded">
                                <img :src="product.image" :alt="product.name" class="w-full h-80 object-contain mb-2">
                                <h4 class="font-semibold">{{ product.name }}</h4>
                                <p class="text-sm text-gray-600 dark:text-gray-400">{{ product.category }}</p>
                                <p class="mt-1">Rp. {{ product.price.toLocaleString('id-ID') }}</p>
                                <p class="text-sm mt-2">{{ product.description }}</p>
                                <button @click="addToCart(product)" class="mt-2 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
                                    Add to Cart
                                </button>
                            </div>
                        </div>

                        <h3 class="text-lg font-semibold mt-8 mb-4">Shopping Cart</h3>
                        <div v-if="cart.length === 0">Your cart is empty</div>
                        <ul v-else>
                            <li v-for="(item, index) in cart" :key="index" class="mb-2 flex justify-between items-center">
                                <span>{{ item.product.name }} - Rp. {{ item.product.price.toLocaleString('id-ID') }} x {{ item.quantity }}</span>
                                <button @click="removeFromCart(index)" class="text-red-500 hover:text-red-700">Remove</button>
                            </li>
                        </ul>
                        <p v-if="cart.length > 0" class="mt-4 font-semibold">
                            Total: Rp. {{ cartTotal }}
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
