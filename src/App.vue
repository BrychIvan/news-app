<script setup>
import { ref, computed, onMounted } from 'vue'
import NewsCard from './components/NewsCard.vue'
import NewsFilters from './components/NewsFilters.vue'
import NewsLoader from './components/NewsLoader.vue'

const API_KEY = '206e541ebc8a41d6ad884a6cbeca9b90'
const API_BASE_URL = 'https://newsapi.org/v2'

// State
const news = ref([])
const loading = ref(false)
const error = ref('')
const searchQuery = ref('')
const selectedCategory = ref('')
const selectedLanguage = ref('uk')
const sortBy = ref('publishedAt')
const page = ref(1)
const totalResults = ref(0)

// Categories for filter
const categories = ['business', 'entertainment', 'general', 'health', 'science', 'sports', 'technology']

// Fetch news from API
const fetchNews = async (pageNum = 1) => {
  loading.value = true
  error.value = ''

  try {
    let url = `${API_BASE_URL}/everything?`

    // Build query params
    const params = new URLSearchParams()

    if (searchQuery.value.trim()) {
      params.append('q', searchQuery.value)
    } else {
      params.append('q', 'news')
    }

    if (selectedCategory.value) {
      params.append('category', selectedCategory.value)
    }

    if (selectedLanguage.value) {
      params.append('language', selectedLanguage.value)
    }

    params.append('sortBy', sortBy.value)
    params.append('pageSize', 12)
    params.append('page', pageNum)

    if (API_KEY !== 'demo') {
      params.append('apiKey', API_KEY)
      url += params.toString()
    } else {
      // Demo mode - fetch from alternative free API
      return fetchDemoNews()
    }

    const response = await fetch(url)

    if (!response.ok) {
      throw new Error('Помилка завантаження новин')
    }

    const data = await response.json()

    if (data.status === 'ok') {
      news.value = data.articles.map((article, index) => ({
        id: index,
        title: article.title,
        description: article.description,
        image: article.urlToImage,
        url: article.url,
        source: article.source.name,
        date: article.publishedAt
      }))
      totalResults.value = data.totalResults
      page.value = pageNum
    } else {
      error.value = data.message || 'Помилка при завантаженні'
    }
  } catch (err) {
    console.error('API Error:', err)
    error.value = 'Неможливо завантажити новини. Перевірте ваш API ключ або інтернет з\'єднання.'
    fetchDemoNews()
  } finally {
    loading.value = false
  }
}

// Demo news for when API is not configured
const fetchDemoNews = () => {
  news.value = [
    {
      id: 1,
      title: 'Новий VueJS версії випущено',
      description: 'Вышла нова версія Vue 3.5 з покращенням продуктивності та меншим розміром bundle',
      source: 'Tech News',
      date: new Date().toISOString(),
      image: 'https://via.placeholder.com/400x200?text=Vue+JS',
      url: '#'
    },
    {
      id: 2,
      title: 'Веб-розробка тренди 2026',
      description: 'Топ 10 трендів веб-розробки на цей рік включають AI інтеграцію та Web Components',
      source: 'Dev Digest',
      date: new Date().toISOString(),
      image: 'https://via.placeholder.com/400x200?text=Web+Trends',
      url: '#'
    },
    {
      id: 3,
      title: 'AI революція в розробці',
      description: 'Як штучний інтелект змінює розробку програм: автоматизація, код-генерація, тестування',
      source: 'AI Weekly',
      date: new Date().toISOString(),
      image: 'https://via.placeholder.com/400x200?text=AI+Dev',
      url: '#'
    },
    {
      id: 4,
      title: 'Основи CSS Grid',
      description: 'Навчитись робити крутні адаптивні макети з CSS Grid Layout',
      source: 'CSS Tricks',
      date: new Date().toISOString(),
      image: 'https://via.placeholder.com/400x200?text=CSS+Grid',
      url: '#'
    }
  ]
}

// Computed properties
const filteredNews = computed(() => {
  return news.value.filter(item => {
    const matchesSearch =
      !searchQuery.value ||
      item.title?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      item.description?.toLowerCase().includes(searchQuery.value.toLowerCase())
    return matchesSearch
  })
})

const hasResults = computed(() => news.value.length > 0)

// Methods
const handleSearch = () => {
  page.value = 1
  fetchNews(1)
}

const handleReset = () => {
  searchQuery.value = ''
  selectedCategory.value = ''
  selectedLanguage.value = 'uk'
  sortBy.value = 'publishedAt'
  page.value = 1
  fetchNews(1)
}

const loadMore = () => {
  fetchNews(page.value + 1)
}

// Lifecycle
onMounted(() => {
  fetchNews()
})
</script>

<template>
  <div class="app">
    <header class="header">
      <div class="container">
        <h1 class="title">NEWS</h1>
        <p class="subtitle">Fresh news from around the world</p>
      </div>
    </header>

    <main class="container">
      <NewsFilters
        :search-query="searchQuery"
        :selected-category="selectedCategory"
        :selected-language="selectedLanguage"
        :sort-by="sortBy"
        :categories="categories"
        @update:search-query="searchQuery = $event; handleSearch()"
        @update:selected-category="selectedCategory = $event; handleSearch()"
        @update:selected-language="selectedLanguage = $event; handleSearch()"
        @update:sort-by="sortBy = $event; handleSearch()"
        @reset="handleReset"
      />

      <div v-if="error" class="error-message">
        <strong>{{ error }}</strong>
        <p style="font-size: 0.9rem; margin-top: 8px;">
          To use real news, get a free API key from
          <a href="https://newsapi.org/" target="_blank">newsapi.org</a>
          and replace the <code>API_KEY</code> value in <code>App.vue</code>
        </p>
      </div>

      <div v-if="loading" class="news-container">
        <NewsLoader message="Loading news..." />
      </div>

      <div v-else-if="hasResults" class="news-container">
        <div class="news-list">
          <NewsCard
            v-for="article in filteredNews"
            :key="article.id"
            :article="article"
          />
        </div>

        <div v-if="totalResults > news.length" class="load-more-container">
          <button @click="loadMore" class="load-more-btn" :disabled="loading">
            {{ loading ? 'Loading...' : 'Load More' }}
          </button>
        </div>
      </div>

      <div v-else class="empty-state">
        <p>No news found</p>
        <p style="margin-top: 10px; color: #adb5bd;">Try changing filters or your search query</p>
      </div>

      <footer class="footer">
        <p>
          Total: <strong>{{ totalResults || 'N/A' }}</strong> |
          Showing: <strong>{{ news.length }}</strong>
        </p>
      </footer>
    </main>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  min-height: 100vh;
  background: #fff;
}

.header {
  background: #fff;
  border-bottom: 1px solid #e5e5e5;
  padding: 30px 0;
  text-align: center;
}

.header .container {
  max-width: 980px;
  margin: 0 auto;
  padding: 0 20px;
}

.title {
  font-size: 2.5rem;
  margin-bottom: 10px;
  font-weight: 700;
  letter-spacing: 2px;
  color: #000;
  font-family: Georgia, serif;
}

.subtitle {
  font-size: 0.95rem;
  color: #666;
  font-weight: 400;
  letter-spacing: 0.5px;
}

.container {
  max-width: 980px;
  margin: 0 auto;
  padding: 0 20px;
}

main {
  padding: 30px 0;
}

.news-container {
  padding: 30px 0;
}

.news-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 30px;
  margin-bottom: 40px;
  border-top: 1px solid #e5e5e5;
  padding-top: 30px;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #999;
  font-size: 1.1rem;
}

.error-message {
  background: #fafafa;
  border-left: 3px solid #d32f2f;
  padding: 15px 20px;
  border-radius: 0;
  margin-bottom: 30px;
  color: #333;
}

.error-message a {
  color: #1976d2;
  text-decoration: underline;
}

.error-message code {
  background: #f5f5f5;
  padding: 2px 6px;
  font-family: monospace;
  font-size: 0.9rem;
}

.load-more-container {
  display: flex;
  justify-content: center;
  padding: 30px 0;
}

.load-more-btn {
  padding: 12px 32px;
  background: #000;
  color: white;
  border: none;
  border-radius: 0;
  font-size: 0.95rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  letter-spacing: 0.5px;
}

.load-more-btn:hover:not(:disabled) {
  background: #333;
}

.load-more-btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.footer {
  text-align: center;
  padding: 30px 20px;
  color: #999;
  border-top: 1px solid #e5e5e5;
  margin-top: 40px;
  font-size: 0.9rem;
}

.footer strong {
  color: #333;
}

@media (max-width: 768px) {
  .title {
    font-size: 1.8rem;
    letter-spacing: 1px;
  }

  .subtitle {
    font-size: 0.9rem;
  }

  .news-list {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  main {
    padding: 20px 0;
  }

  .news-container {
    padding: 20px 0;
  }
}
</style>
