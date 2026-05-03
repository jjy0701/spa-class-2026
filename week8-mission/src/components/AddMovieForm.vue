<script setup>
import { ref, computed } from 'vue';

const emit = defineEmits(['add-movie']);

// 폼 상태 관리 변수
const newTitle = ref('');
const newRating = ref('');
const newTags = ref([]);
const tagInput = ref('');

// 파일 업로드 관련 상태
const posterBase64 = ref(null);
const fileInputRef = ref(null);

// 이미지를 Base64 문자열로 변환하는 핵심 로직
const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = (e) => {
    posterBase64.value = e.target.result;
  };
  reader.readAsDataURL(file);
};

// 숨겨진 파일 input을 대신 클릭해주는 함수
const triggerFileInput = () => {
  fileInputRef.value.click();
};

// 해시태그 관리 로직
const addTag = () => {
  const val = tagInput.value.trim();
  if (val && !newTags.value.includes(val)) {
    newTags.value.push(val);
  }
  tagInput.value = '';
};

const removeTag = (index) => {
  newTags.value.splice(index, 1);
};

const submitMovie = () => {
  //빈 값 방어 및 alert() 알림
  if (newTitle.value.trim() === '' || newRating.value === '') {
    alert('영화 제목과 평점을 모두 입력해주세요!');
    return; // 실행 중단
  }

  if (newRating.value < 0 || newRating.value > 10) {
    alert('평점은 0에서 10 사이로 입력해주세요.');
    return;
  }

  if (!posterBase64.value) {
    alert('포스터 이미지를 업로드해주세요.');
    return;
  }

  emit('add-movie', {
    id: Date.now(), 
    title: newTitle.value,
    rating: Number(newRating.value),
    likes: 0,
    poster: posterBase64.value,
    tags: [...newTags.value]
  });

  // 초기화
  newTitle.value = '';
  newRating.value = '';
  newTags.value = [];
  posterBase64.value = null;
  if (fileInputRef.value) fileInputRef.value.value = '';
};

</script>

<template>
  <div class="add-form-container">
    <h3 class="form-title">🎬 나만의 영화 등록하기</h3>
    
    <div class="form-grid">
      <div class="poster-upload" @click="triggerFileInput" :class="{ 'has-image': posterBase64 }">
        <img v-if="posterBase64" :src="posterBase64" alt="포스터 미리보기" class="poster-preview" />
        <div v-else class="upload-placeholder">
          <span class="plus-icon">+</span>
          <p>클릭하여 포스터 업로드</p>
        </div>
        <input type="file" accept="image/*" ref="fileInputRef" @change="handleImageUpload" class="hidden-input" />
      </div>

      <div class="input-area">
        <input v-model="newTitle" type="text" placeholder="영화 제목" class="form-input title-input" />
        
        <div class="rating-input-wrapper">
          <label>평점:</label>
          <input v-model="newRating" type="number" step="0.1" min="0" max="10" placeholder="0~10" class="form-input rating-input" />
        </div>

        <div class="tags-container">
          <span v-for="(tag, index) in newTags" :key="index" class="tag">
            #{{ tag }} <button @click="removeTag(index)" class="remove-tag">×</button>
          </span>
        </div>
        <input v-model="tagInput" @keyup.enter="addTag" type="text" placeholder="엔터로 해시태그 추가" class="form-input" />

        <button class="submit-btn" @click="submitMovie">새 영화 등록</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.add-form-container { background: #fff; border: 1px solid #e0e0e0; border-radius: 12px; padding: 25px; margin-bottom: 40px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
.form-title { color: #2c3e50; font-size: 1.4rem; margin-top: 0; margin-bottom: 20px; text-align: center; font-weight: 800; }

.form-grid { display: flex; gap: 20px; align-items: stretch; }
@media (max-width: 600px) { .form-grid { flex-direction: column; } }

.poster-upload { flex: 1; border: 2px dashed #bdc3c7; border-radius: 8px; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: all 0.2s ease; overflow: hidden; position: relative; min-height: 250px; background-color: #f8f9fa; }
.poster-upload:hover { border-color: #3498db; background-color: #ebf5fb; }
.poster-upload.has-image { border: none; }
.poster-preview { width: 100%; height: 100%; object-fit: cover; position: absolute; top: 0; left: 0; }
.upload-placeholder { text-align: center; color: #7f8c8d; }
.plus-icon { font-size: 2.5rem; font-weight: bold; color: #bdc3c7; display: block; margin-bottom: 5px; }
.hidden-input { display: none; }

.input-area { flex: 2; display: flex; flex-direction: column; justify-content: space-between; gap: 12px; }
.form-input { width: 100%; padding: 12px; border: 1px solid #bdc3c7; border-radius: 6px; font-size: 1rem; box-sizing: border-box; font-family: inherit; }
.title-input { font-weight: bold; font-size: 1.1rem; }
.rating-input-wrapper { display: flex; align-items: center; gap: 10px; font-weight: bold; color: #34495e; }
.rating-input { width: 100px; }

.tags-container { display: flex; flex-wrap: wrap; gap: 6px; min-height: 28px; }
.tag { background-color: #ecf0f1; padding: 5px 12px; border-radius: 15px; font-size: 0.9rem; color: #34495e; display: flex; align-items: center; }
.remove-tag { border: none; background: transparent; cursor: pointer; font-weight: bold; color: #e74c3c; margin-left: 5px; font-size: 1.1rem; padding: 0; }

.submit-btn { background-color: #3498db; color: white; border: none; padding: 14px; border-radius: 6px; font-size: 1.1rem; font-weight: bold; cursor: pointer; transition: background-color 0.2s; margin-top: 10px; }
.submit-btn:hover:not(:disabled) { background-color: #2980b9; }
.submit-btn:disabled { background-color: #bdc3c7; cursor: not-allowed; }
</style>