<script setup>
defineProps({
  title: {
    type: String,
    required: true
  },
  count: {
    type: Number,
    default: 0
  }
});
</script>

<template>
  <header class="page-header">
    <h1>{{ title }}</h1>
    <div class="count-badge">현재 상영작: {{ count }}개</div>
  </header>
</template>

<style scoped>
.page-header { 
  text-align: center; 
  margin-bottom: 50px; 
  padding: 30px; 
  border-bottom: 3px solid #34495e; 
}
.page-header h1 { 
  font-size: 2.5rem; 
  color: #2c3e50; 
  margin-bottom: 15px; 
}
.count-badge { 
  display: inline-block; 
  background: #e74c3c; 
  color: white; 
  padding: 8px 20px; 
  border-radius: 30px; 
  font-weight: bold; 
}
</style>