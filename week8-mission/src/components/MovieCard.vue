<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  movie: { type: Object, required: true }
});

const emit = defineEmits(['like-movie', 'delete-movie', 'update-movie']);

// 컴포넌트 내부 상태 관리
const isEditing = ref(false);
const editTitle = ref('');
const editRating = ref(0);

// 해시태그 상태 관리 (배열)
const editTags = ref([]);
const newTag = ref('');

// 별점 마우스 호버 상태 관리
const hoverRating = ref(0);

// 수정 모드 진입 및 초기화
const startEdit = () => {
  editTitle.value = props.movie.title;
  editRating.value = props.movie.rating;
  // 부모 데이터에 태그가 있으면 복사, 없으면 빈 배열 할당
  editTags.value = props.movie.tags ? [...props.movie.tags] : [];
  isEditing.value = true;
};

// 해시태그 추가 로직 (엔터키 입력 시)
const addTag = () => {
  const tagValue = newTag.value.trim();
  // 빈 값이 아니고, 중복된 태그가 아닐 경우에만 배열에 추가
  if (tagValue && !editTags.value.includes(tagValue)) {
    editTags.value.push(tagValue);
  }
  newTag.value = ''; // 입력창 초기화
};

// 해시태그 삭제 로직
const removeTag = (index) => {
  editTags.value.splice(index, 1);
};

// 별점 클릭 시 점수 고정 로직
const setRating = (val) => {
  editRating.value = val;
};

// 폼 유효성 실시간 검사
const isFormValid = computed(() => {
  const isTitleValid = editTitle.value.trim().length > 0;
  const isRatingValid = editRating.value !== '' && editRating.value >= 0 && editRating.value <= 10;
  return isTitleValid && isRatingValid;
});

// 수정 데이터 부모 컴포넌트로 전달
const saveEdit = () => {
  if (!isFormValid.value) return;

  emit('update-movie', {
    id: props.movie.id,
    title: editTitle.value,
    rating: editRating.value,
    tags: editTags.value // 생성된 태그 배열 데이터 추가
  });
  isEditing.value = false;
};

const cancelEdit = () => {
  isEditing.value = false;
};
</script>

<template>
  <div class="card">
    <div class="poster-area">
      <img :src="movie.poster" :alt="movie.title" class="poster">
    </div>
    
    <div class="content-area">
      <div v-if="isEditing">
        <input v-model="editTitle" type="text" class="edit-input" placeholder="영화 제목" />
        
        <div class="star-rating-edit" @mouseleave="hoverRating = 0">
          <span
            v-for="i in 10"
            :key="i"
            class="star"
            :class="{ active: i <= (hoverRating || editRating) }"
            @mouseover="hoverRating = i"
            @click="setRating(i)"
          >★</span>
          <div class="rating-text">{{ hoverRating || editRating }} / 10</div>
        </div>

        <div class="hashtag-edit">
          <div class="tags-container">
            <span v-for="(tag, index) in editTags" :key="index" class="tag">
              #{{ tag }} 
              <button class="remove-tag" @click="removeTag(index)">×</button>
            </span>
          </div>
          <input
            v-model="newTag"
            @keyup.enter="addTag"
            type="text"
            class="edit-input tag-input"
            placeholder="엔터로 태그 추가 (예: 인생영화)"
          />
        </div>
        
        <p v-if="!isFormValid" class="error-msg">정확한 제목과 평점(1~10)을 입력하세요.</p>
        
        <div class="btn-group">
          <button class="btn save-btn" :disabled="!isFormValid" @click="saveEdit">저장</button>
          <button class="btn cancel-btn" @click="cancelEdit">취소</button>
        </div>
      </div>

      <div v-else>
        <h3>{{ movie.title }}</h3>
        
        <div class="tags-container" v-if="movie.tags && movie.tags.length">
          <span v-for="(tag, index) in movie.tags" :key="index" class="tag">#{{ tag }}</span>
        </div>

        <p class="rating">평점: {{ movie.rating }} / 10</p>
        <p class="likes">좋아요: {{ movie.likes }}</p>
        <div class="btn-group">
          <button class="btn like-btn" @click="$emit('like-movie', movie.id)">추천</button>
          <button class="btn edit-btn" @click="startEdit">수정</button>
          <button class="btn del-btn" @click="$emit('delete-movie', movie.id)">삭제</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card { 
  flex: 0 0 280px; 
  background: #fff; 
  border: 1px solid #e0e0e0; 
  border-radius: 12px; 
  overflow: hidden; 
  box-shadow: 0 10px 20px rgba(0,0,0,0.1); 
  transform-style: preserve-3d;
  transition: transform 0.5s cubic-bezier(0.25, 1, 0.5, 1), 
              box-shadow 0.5s ease, 
              opacity 0.5s ease, 
              filter 0.5s ease;
}

.card:hover { 
  transform: translateZ(60px) scale(1.1) translateY(-10px); 
  box-shadow: 0 30px 60px rgba(0,0,0,0.4); 
  z-index: 10; 
}
.poster-area { position: relative; width: 100%; height: 300px; }
.poster { width: 100%; height: 100%; object-fit: cover; }
.content-area { padding: 20px; text-align: center; }
h3 { margin: 0 0 10px 0; color: #2c3e50; font-size: 1.4rem; }
.rating { font-weight: 600; color: #f39c12; margin: 5px 0; }
.likes { font-weight: 600; color: #e74c3c; margin: 5px 0 20px 0; }

.btn-group { display: flex; gap: 10px; justify-content: center; margin-top: 15px; }
.btn { padding: 10px 15px; cursor: pointer; border: none; border-radius: 6px; font-weight: bold; font-size: 0.9rem; transition: opacity 0.2s; }
.btn:hover:not(:disabled) { opacity: 0.8; }
.like-btn { background-color: #3498db; color: white; flex: 1; }
.del-btn { background-color: #e74c3c; color: white; flex: 1; }
.edit-btn { background-color: #2ecc71; color: white; flex: 1; }
.save-btn { background-color: #f39c12; color: white; flex: 1; }
.save-btn:disabled { background-color: #bdc3c7; cursor: not-allowed; }
.cancel-btn { background-color: #95a5a6; color: white; flex: 1; }

.edit-input { width: 100%; padding: 8px; margin-bottom: 10px; border: 1px solid #bdc3c7; border-radius: 4px; font-size: 1rem; box-sizing: border-box; text-align: center; font-family: inherit; }
.error-msg { color: #e74c3c; font-size: 0.85rem; margin: 0 0 10px 0; font-weight: bold; }

/* 별점 UI 스타일 */
.star-rating-edit { margin-bottom: 15px; }
.star { cursor: pointer; color: #e0e0e0; font-size: 1.8rem; transition: color 0.2s; display: inline-block; }
.star.active { color: #f1c40f; }
.rating-text { font-size: 0.9rem; font-weight: bold; color: #7f8c8d; margin-top: 5px; }

/* 해시태그 UI 스타일 */
.hashtag-edit { margin-bottom: 15px; }
.tags-container { display: flex; flex-wrap: wrap; gap: 6px; justify-content: center; margin-bottom: 10px; min-height: 24px; }
.tag { background-color: #ecf0f1; padding: 4px 10px; border-radius: 15px; font-size: 0.85rem; color: #34495e; display: flex; align-items: center; }
.remove-tag { border: none; background: transparent; cursor: pointer; font-weight: bold; color: #e74c3c; margin-left: 4px; font-size: 1rem; padding: 0; line-height: 1; }
.tag-input { font-size: 0.85rem; padding: 6px; }

</style>