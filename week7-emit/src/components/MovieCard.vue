<script setup>
// 1. 부모로부터 수신할 데이터(Props)
defineProps({
  movie: {
    type: Object,
    required: true
  }
});

// 2. 부모에게 발신할 이벤트(Emit) 명세
defineEmits(['like-movie', 'delete-movie']);
</script>

<template>
  <div class="card">
    <div class="poster-area">
      <img :src="movie.poster" :alt="movie.title" class="poster">
    </div>
    
    <div class="content-area">
      <h3>{{ movie.title }}</h3>
      <p class="rating">평점: {{ movie.rating }} / 10</p>
      <p class="likes">좋아요: {{ movie.likes }}</p>
      
      <div class="btn-group">
        <button class="btn like-btn" @click="$emit('like-movie', movie.id)">추천</button>
        <button class="btn del-btn" @click="$emit('delete-movie', movie.id)">삭제</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card { 
  background: #fff; 
  border: 1px solid #e0e0e0; 
  border-radius: 12px; 
  overflow: hidden; 
  box-shadow: 0 4px 6px rgba(0,0,0,0.05); 
  transition: transform 0.2s ease;
}
.card:hover { 
  transform: translateY(-5px); 
  box-shadow: 0 8px 15px rgba(0,0,0,0.1); 
}

.poster-area { 
  position: relative; 
  width: 100%; 
  height: 300px; 
}
.poster { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
}

.content-area {
  padding: 20px;
  text-align: center;
}
h3 { 
  margin: 0 0 10px 0; 
  color: #2c3e50; 
  font-size: 1.4rem;
}
.rating { 
  font-weight: 600; 
  color: #f39c12; 
  margin: 5px 0;
}
.likes { 
  font-weight: 600; 
  color: #e74c3c; 
  margin: 5px 0 20px 0;
}

.btn-group { 
  display: flex; 
  gap: 10px; 
  justify-content: center; 
}
.btn { 
  padding: 10px 15px; 
  cursor: pointer; 
  border: none; 
  border-radius: 6px; 
  font-weight: bold; 
  font-size: 0.9rem;
  transition: opacity 0.2s;
}
.btn:hover { 
  opacity: 0.8; 
}
.like-btn { 
  background-color: #3498db; 
  color: white; 
  flex: 1;
}
.del-btn { 
  background-color: #e74c3c; 
  color: white; 
  flex: 1;
}
</style>