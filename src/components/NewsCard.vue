<template>
  <article class="news-card">
    <div class="news-image">
      <img
        v-if="article.image"
        :src="article.image"
        :alt="article.title"
        class="image"
      />
    </div>
    <div class="news-content">
      <h2 class="news-title">{{ article.title }}</h2>
      <p v-if="article.description" class="news-description">
        {{ truncateText(article.description, 120) }}
      </p>
      <div class="news-meta">
        <span v-if="article.source" class="source">{{ article.source }}</span>
        <span class="date">{{ formatDate(article.date) }}</span>
      </div>
      <a
        v-if="article.url"
        :href="article.url"
        target="_blank"
        class="read-more"
      >
        Read full article
      </a>
    </div>
  </article>
</template>

<script setup>
defineProps({
  article: {
    type: Object,
    required: true
  }
})

const formatDate = (date) => {
  if (!date) return ''
  const options = { year: 'numeric', month: 'short', day: 'numeric' }
  return new Date(date).toLocaleDateString('en-US', options)
}

const truncateText = (text, length) => {
  if (!text) return ''
  return text.length > length ? text.substring(0, length) + '...' : text
}
</script>

<style scoped>
.news-card {
  background: #fff;
  overflow: hidden;
  box-shadow: none;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  height: 100%;
  border-bottom: 1px solid #e5e5e5;
  padding-bottom: 30px;
}

.news-card:hover {
  box-shadow: none;
}

.news-image {
  width: 100%;
  height: 160px;
  overflow: hidden;
  background: #f5f5f5;
  margin-bottom: 15px;
}

.image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.news-card:hover .image {
  transform: scale(1.02);
}

.news-content {
  padding: 0;
  display: flex;
  flex-direction: column;
  flex: 1;
}

.news-title {
  font-size: 1.3rem;
  color: #000;
  margin-bottom: 12px;
  line-height: 1.4;
  font-weight: 600;
  font-family: Georgia, serif;
}

.news-description {
  color: #666;
  font-size: 0.95rem;
  line-height: 1.6;
  margin-bottom: 15px;
  flex: 1;
}

.news-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  gap: 10px;
  flex-wrap: wrap;
  font-size: 0.85rem;
}

.source {
  display: inline-block;
  color: #666;
  font-size: 0.8rem;
  font-weight: 500;
}

.date {
  font-size: 0.85rem;
  color: #999;
}

.read-more {
  color: #1976d2;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.3s ease;
  display: inline-block;
  font-size: 0.9rem;
}

.read-more:hover {
  color: #0d47a1;
  text-decoration: underline;
}
</style>
