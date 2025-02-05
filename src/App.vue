<script setup lang="ts">
import { ref } from 'vue'

const searchTerm = ref('')
const results = ref([])

const performSearch = async () => {
  if (searchTerm.value.length > 2) {
    try {
      const response = await fetch(`http://localhost:3000/api/search?q=${searchTerm.value}`)
      console.log(response)
      const data = await response.json()
      results.value = data.results
    } catch (error) {
      console.error('Error fetching results:', error)
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

  <!-- Add this section to display results -->
  <div class="results-container">
    <div v-for="storeResult in results" :key="storeResult.store">
      <h2>{{ storeResult.store }}</h2>
      <div class="products-grid">
        <div v-for="product in storeResult.products" :key="product.id" class="product-card">
          <img v-if="product.imageUrl" :src="product.imageUrl" :alt="product.name" />
          <h3>{{ product.name }}</h3>
          <p class="price">{{ product.price }} kr</p>
          <a :href="product.url" target="_blank" rel="noopener">View Product</a>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.search-container {
  width: 100%;
  max-width: 600px;
  margin: 2rem auto;
  padding: 0 1rem;
  display: flex;
  gap: 0.5rem;
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
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
  margin-top: 1rem;
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
</style>
