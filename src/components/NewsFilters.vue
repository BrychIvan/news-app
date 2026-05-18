<template>
  <div class="filters">
    <div class="search-box">
      <input
        :value="searchQuery"
        @input="$emit('update:searchQuery', $event.target.value)"
        type="text"
        placeholder="Search news..."
        class="search-input"
      />
    </div>

    <div class="filter-group">
      <select
        :value="selectedLanguage"
        @change="$emit('update:selectedLanguage', $event.target.value)"
        class="filter-select"
      >
        <option value="en">English</option>
        <option value="uk">Ukrainian</option>
      </select>

      <select
        :value="sortBy"
        @change="$emit('update:sortBy', $event.target.value)"
        class="filter-select"
      >
        <option value="publishedAt">Newest</option>
        <option value="relevancy">Relevance</option>
        <option value="popularity">Popularity</option>
      </select>

      <button @click="$emit('reset')" class="reset-btn">
        Reset filters
      </button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  searchQuery: String,
  selectedCategory: String,
  selectedLanguage: String,
  sortBy: String,
  categories: {
    type: Array,
    default: () => [
      'business',
      'entertainment',
      'general',
      'health',
      'science',
      'sports',
      'technology'
    ]
  }
})

defineEmits(['update:searchQuery', 'update:selectedCategory', 'update:selectedLanguage', 'update:sortBy', 'reset'])
</script>

<style scoped>
.filters {
  padding: 20px 0;
  margin-bottom: 30px;
  border-bottom: 1px solid #e5e5e5;
}

.search-box {
  margin-bottom: 20px;
}

.search-input {
  width: 100%;
  padding: 12px 14px;
  font-size: 1rem;
  border: 1px solid #ddd;
  border-radius: 0;
  outline: none;
  transition: all 0.3s ease;
  background: white;
  box-shadow: none;
}

.search-input:focus {
  border-color: #333;
  box-shadow: none;
}

.filter-group {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  align-items: center;
}

.filter-select {
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 0;
  background: white;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #333;
}

.filter-select:hover {
  border-color: #333;
}

.filter-select:focus {
  outline: none;
  border-color: #333;
  box-shadow: none;
}

.reset-btn {
  padding: 10px 16px;
  border: 1px solid #ddd;
  background: white;
  border-radius: 0;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #333;
  font-weight: 500;
  font-size: 0.9rem;
}

.reset-btn:hover {
  border-color: #333;
  background: #f5f5f5;
}

@media (max-width: 768px) {
  .filter-group {
    flex-direction: column;
  }

  .filter-select {
    width: 100%;
  }

  .reset-btn {
    width: 100%;
  }
}
</style>
