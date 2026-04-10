<script setup>
import { ref } from 'vue';
import PageHeader from './components/PageHeader.vue';
import MovieCard from './components/MovieCard.vue';

// 심화 데이터 선언: 더미 이미지 URL과 다양한 평점 추가
const movies = ref([
  { 
    id: 1, title: '인셉션', rating: 9.5,
    poster: 'https://picsum.photos/seed/inception/300/450',
    isHot: true, isNew: false, tags: ['SF', '명작']
  },
  { 
    id: 2, title: '어바웃 타임', rating: 9.2,
    poster: 'https://picsum.photos/seed/abouttime/300/450',
    isHot: false, isNew: false, tags: ['로맨스', '인생영화']
  },
  { 
    id: 3, title: '다크 나이트', rating: 9.8,
    poster: 'https://picsum.photos/seed/darkknight/300/450',
    isHot: true, isNew: true, tags: ['조커', '히어로']
  },
  { 
    id: 4, title: '기생충', rating: 8.9,
    poster: 'https://picsum.photos/seed/parasite/300/450',
    isHot: false, isNew: true, tags: ['스릴러', '봉준호']
  }
]);
</script>

<template>
  <div class="container">
    <PageHeader title="6주차 영화 추천 시스템" :count="movies.length" />
    
    <main class="movie-grid">
      <MovieCard v-for="m in movies" :key="m.id" :movie="m" />
    </main>
  </div>
</template>

<style scoped>
.container { 
  padding: 50px; 
  max-width: 1100px; 
  margin: 0 auto; 
  background-color: #f1f2f6; 
  min-height: 100vh; 
}
.movie-grid { 
  display: grid; 
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); 
  gap: 35px; 
}
</style>