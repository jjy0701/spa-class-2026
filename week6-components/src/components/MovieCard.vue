<script setup>
defineProps({
  movie: { 
    type: Object, 
    required: true 
  }
});
</script>

<template>
  <div class="card">
    <div class="poster-area">
      <img :src="movie.poster" :alt="movie.title" class="poster">
      <span v-if="movie.rating >= 9.5" class="recommend best">강력 추천</span>
      <span v-else-if="movie.rating >= 9.2" class="recommend soso">SOSO</span>
      <span v-else class="recommend poor">글쎄</span>
    </div>
    <div class="content-area">
      <div class="badge-area">
        <span v-if="movie.isHot" class="badge hot">HOT</span>
        <span v-if="movie.isNew" class="badge new">NEW</span>
      </div>
      <h3>{{ movie.title }}</h3>
      <p class="rating">평점: ★ {{ movie.rating }}</p>
      <div class="tags">
        <span v-for="(tag, index) in movie.tags" :key="index" class="tag-item">
          #{{ tag }}
        </span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card { 
  background: #fff; 
  border-radius: 15px; 
  overflow: hidden; 
  box-shadow: 0 10px 20px rgba(0,0,0,0.1); 
  transition: transform 0.3s ease, box-shadow 0.3s ease; 
}
.card:hover { 
  transform: translateY(-10px); 
  box-shadow: 0 15px 25px rgba(0,0,0,0.15); 
}
.poster-area { 
  position: relative; 
  width: 100%; 
  height: 350px; 
}
.poster { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
}
/* 추천 태그 스타일 */
.recommend { 
  position: absolute; 
  top: 15px; 
  left: 15px; 
  padding: 5px 12px; 
  border-radius: 5px; 
  color: white; 
  font-weight: bold; 
  font-size: 0.85rem; 
}
.best { background: #e74c3c; }
.soso { background: #f39c12; }
.poor { background: #95a5a6; }
.content-area { 
  padding: 20px; 
  text-align: left; 
}
.badge-area { 
  margin-bottom: 10px; 
  display: flex; 
  gap: 5px; 
}
.badge { 
  font-size: 0.7rem; 
  padding: 2px 6px; 
  border-radius: 3px; 
  color: white; 
  font-weight: bold;
}
.hot { background: #ff4757; }
.new { background: #2f3542; }
h3 { 
  margin: 0 0 10px 0; 
  font-size: 1.3rem; 
  color: #2f3542; 
}
.rating { 
  font-weight: bold; 
  color: #7f8c8d; 
  margin-bottom: 15px; 
}
.tags { 
  display: flex; 
  gap: 5px; 
  flex-wrap: wrap; 
}
.tag-item { 
  color: #3498db; 
  font-size: 0.8rem; 
  background: #f1f2f6; 
  padding: 3px 8px; 
  border-radius: 5px; 
}
</style>