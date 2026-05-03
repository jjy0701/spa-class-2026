<script setup>
import { ref, computed, watch, onMounted } from 'vue';
import MovieCard from './components/MovieCard.vue';
import AddMovieForm from './components/AddMovieForm.vue';

// 시스템의 기준이 되는 초기 영화 데이터 정의
const INITIAL_MOVIES = [
  { id: 1, title: '인셉션', rating: 9.5, likes: 0, poster: 'https://picsum.photos/seed/inception/300/450', tags: [] },
  { id: 2, title: '어바웃 타임', rating: 9.2, likes: 0, poster: 'https://picsum.photos/seed/abouttime/300/450', tags: [] },
  { id: 3, title: '다크 나이트', rating: 9.2, likes: 0, poster: 'https://picsum.photos/seed/darknight/300/450', tags: [] },
  { id: 4, title: '기생충', rating: 8.9, likes: 0, poster: 'https://picsum.photos/seed/parasite/300/450', tags: [] }
];

const movies = ref([]);

// 컴포넌트 마운트 시 로컬 스토리지에서 데이터를 로드하고 없으면 초기 데이터를 할당함
onMounted(() => {
  const savedMovies = localStorage.getItem('my_movies');
  if (savedMovies) {
    movies.value = JSON.parse(savedMovies);
  } else {
    movies.value = JSON.parse(JSON.stringify(INITIAL_MOVIES));
  }
});

// 데이터 변경 시마다 로컬 스토리지에 실시간으로 저장함
watch(movies, (newVal) => {
  localStorage.setItem('my_movies', JSON.stringify(newVal));
}, { deep: true });


const handleAddMovie = (newMovie) => {
  // unshift를 사용하면 새로 등록한 영화가 리스트의 맨 앞에 배치됩니다.
  movies.value.unshift(newMovie); 
};

// 로컬 스토리지를 비우고 모든 상태를 초기 데이터로 복구하는 함수
const handleReset = () => {
  if (confirm('모든 수정 사항을 삭제하고 초기 상태로 되돌리시겠습니까?')) {
    localStorage.removeItem('my_movies');
    movies.value = JSON.parse(JSON.stringify(INITIAL_MOVIES));
  }
};

const sortOption = ref('default');

// 선택된 정렬 기준에 따라 새로운 배열을 반환하는 계산된 속성
const sortedMovies = computed(() => {
  const tempArray = [...movies.value];
  
  if (sortOption.value === 'rating') {
    return tempArray.sort((a, b) => {
      if (b.rating === a.rating) {
        // 평점이 같으면 제목 기준 가나다(오름차순) 정렬
        return a.title.localeCompare(b.title);
      }
      // 평점이 다르면 평점 높은 순(내림차순) 정렬
      return b.rating - a.rating;
    });
  } 
  else if (sortOption.value === 'likes') {
    return tempArray.sort((a, b) => {
      if (b.likes === a.likes) {
        // 좋아요가 같으면 제목 기준 가나다(오름차순) 정렬
        return a.title.localeCompare(b.title);
      }
      // 좋아요가 다르면 좋아요 많은 순(내림차순) 정렬
      return b.likes - a.likes;
    });
  }
  
  return tempArray;
});

const handleLike = (targetId) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) movie.likes++;
};

const handleDelete = (targetId) => {
  movies.value = movies.value.filter(m => m.id !== targetId);
};

const handleUpdate = (updatedData) => {
  const movie = movies.value.find(m => m.id === updatedData.id);
  if (movie) {
    movie.title = updatedData.title;
    movie.rating = updatedData.rating;
    movie.tags = updatedData.tags;
  }
};
</script>

<template>
  <div class="container">
    <div class="header-section">
      <h2>상호작용이 추가된 영화 리스트</h2>
      <button class="reset-btn" @click="handleReset">전체 초기화</button>
    </div>
    <AddMovieForm @add-movie="handleAddMovie" />
    
    <div class="sort-header">
      <button 
        class="sort-btn" 
        :class="{ active: sortOption === 'rating' }" 
        @click="sortOption = 'rating'">
        🏆 평점 높은 순
      </button>
      <button 
        class="sort-btn" 
        :class="{ active: sortOption === 'likes' }" 
        @click="sortOption = 'likes'">
        ❤️ 좋아요 많은 순
      </button>
    </div>
    
    <main class="movie-grid">
      <MovieCard
        v-for="m in sortedMovies"
        :key="m.id"
        :movie="m"
        @like-movie="handleLike"
        @delete-movie="handleDelete"
        @update-movie="handleUpdate"
      />
    </main>
  </div>
</template>

<style scoped>
.container { padding: 40px; font-family: sans-serif; max-width: 1000px; margin: 0 auto; background-color: #f8f9fa; min-height: 100vh; overflow-x: hidden;}

.header-section { display: flex; flex-direction: column; align-items: center; margin-bottom: 20px; }
h2 { margin-bottom: 15px; color: #34495e; font-weight: 800; }

/* 초기화 버튼 스타일 */
.reset-btn { background: none; border: 1px solid #bdc3c7; color: #95a5a6; padding: 5px 12px; border-radius: 4px; cursor: pointer; font-size: 0.8rem; transition: all 0.2s; }
.reset-btn:hover { background-color: #e74c3c; color: white; border-color: #e74c3c; }

.sort-header { display: flex; justify-content: center; gap: 15px; margin-bottom: 40px; }
.sort-btn { padding: 10px 20px; font-size: 1rem; font-weight: bold; color: #7f8c8d; background-color: white; border: 2px solid #bdc3c7; border-radius: 30px; cursor: pointer; transition: all 0.3s ease; }
.sort-btn:hover { background-color: #ecf0f1; }
.sort-btn.active { color: white; background-color: #34495e; border-color: #34495e; box-shadow: 0 4px 10px rgba(52, 73, 94, 0.3); }

.movie-grid { 
  display: flex; 
  overflow-x: auto; 
  gap: 30px; 
  padding: 50px 20px; 
  perspective: 1500px; 
  scrollbar-width: none; 
  -ms-overflow-style: none; 
  width: max-content;
  animation: auto-scroll 20s linear infinite alternate;
}

.movie-grid::-webkit-scrollbar {
  display: none;
}
.movie-grid:hover {
  animation-play-state: paused;
}

.movie-grid:hover :deep(.card:not(:hover)) {
  transform: translateZ(-50px) rotateY(15deg) scale(0.9);
  opacity: 0.5;
  filter: blur(2px);
}

@keyframes auto-scroll {
  0% { transform: translateX(0); }
  /* 전체 컨테이너 너비에서 화면에 보이는 영역만큼만 빼서 왼쪽으로 이동 */
  100% { transform: translateX(calc(-100% + 1000px)); } 
}
</style>

