<script setup lang="ts">
import { ref } from 'vue'

const searchTerm = ref('')
const results = ref([])
const isLoading = ref(false)

const performSearch = async () => {
  if (searchTerm.value.length > 2) {
    isLoading.value = true
    try {
      const response = await fetch(`http://localhost:3000/api/search?q=${searchTerm.value}`)
      const data = await response.json()
      results.value = data.results
    } catch (error) {
      console.error('Error fetching results:', error)
    } finally {
      isLoading.value = false
    }
  }
}
</script>

<template>
  <div class="search-container">
    <input
      v-model="searchTerm"
      type="text"
      placeholder="Sök efter produkter..."
      class="search-input"
    />
    <button @click="performSearch" class="search-button">Sök</button>
  </div>

  <div v-if="isLoading" class="loading-spinner">
    <div class="spinner"></div>
  </div>

  <div class="results-container">
    <div v-for="storeResult in results" :key="storeResult.store">
      <h2>{{ storeResult.store }}</h2>
      <div class="products-grid">
        <div v-for="product in storeResult.products" :key="product.id" class="product-card">
          <img v-if="product.imageUrl" :src="product.imageUrl" :alt="product.name" />
          <h3>{{ product.name }}</h3>
          <p class="compare-price">{{ product.comparePrice }}</p>
          <p class="price">{{ product.price }} kr</p>
          <a :href="product.url" target="_blank" rel="noopener">Gå till produkten</a>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.search-container {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: 1rem;
  display: flex;
  gap: 0.5rem;
  position: fixed;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  z-index: 100;
  background-color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
/* Add padding to the body to prevent content from hiding behind the fixed search bar */
body {
  padding-top: 80px;
}

.search-input {
  width: 100%;
  padding: 0.8rem;
  font-size: 1.1rem;
  border: 2px solid #ccc;
  border-radius: 8px;
  outline: none;
  transition: border-color 0.2s;
}

.search-input:focus {
  border-color: #42b883;
}

.search-button {
  padding: 0.8rem 1.5rem;
  font-size: 1.1rem;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.search-button:hover {
  background-color: #3aa876;
}

/* Add these styles */
.results-container {
  max-width: 1200px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
  margin-top: 1rem;
}

@media (max-width: 1200px) {
  .products-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (max-width: 900px) {
  .products-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 600px) {
  .products-grid {
    grid-template-columns: 1fr;
  }
}

.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  text-align: center;
}

.product-card img {
  max-width: 100%;
  height: auto;
}

.price {
  font-weight: bold;
  color: #42b883;
}

.compare-price {
  font-size: 0.8rem;
  color: #42b883;
}

.loading-spinner {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(255, 255, 255, 0.8);
  z-index: 1000;
}

.spinner {
  width: 50px;
  height: 50px;
  border: 5px solid #f3f3f3;
  border-top: 5px solid #3aa876;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
