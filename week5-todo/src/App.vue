<script setup>
import { ref } from 'vue';

// 1. 사용자 입력을 바인딩할 상태 변수
const newTodo = ref('');

// 2. 리스트 렌더링의 기준이 될 객체 배열
const todos = ref([
  { id: 1, text: 'Vue.js 컴포넌트 복습', done: false },
  { id: 2, text: 'UI/UX 기획안 작성', done: true },
  { id: 3, text: '알고리즘 문제 풀이', done: false }
]);

// 3. 데이터 삽입 시 부여할 고유 ID
let nextId = 4;

// Step 3: 데이터 삽입 로직
const addTodo = () => {
  // 입력값이 비어있을 경우 데이터 삽입 차단 (방어 로직)
  if (newTodo.value === '') return;

  // 배열에 새로운 객체 push
  todos.value.push({
    id: nextId++,       // 현재 nextId 부여 후 1 증가
    text: newTodo.value,
    done: false         // 초기 완료 상태는 항상 false
  });

  // 입력 완료 후 input 필드 초기화
  newTodo.value = '';
};

// Step 5: 데이터 삭제 로직
const removeTodo = (targetId) => {
  // 전달받은 targetId와 '같지 않은' 데이터만 남겨서 덮어씌움
  todos.value = todos.value.filter(todo => todo.id !== targetId);
};
</script>

<template>
  <div class="todo-container">
    <h1>To-Do List</h1>
    
    <div class="input-area">
      <input 
        type="text" 
        v-model.trim="newTodo" 
        @keyup.enter="addTodo" 
        placeholder="새로운 할 일을 입력 후 엔터"
      >
      <button @click="addTodo">추가</button>
    </div>
    
    <ul class="todo-list">
      <li v-for="todo in todos" :key="todo.id" class="todo-item">
        <input type="checkbox" v-model="todo.done" class="checkbox">
        <span :class="{ completed: todo.done }" class="text">
          {{ todo.text }}
        </span>
        <button class="delete-btn" @click="removeTodo(todo.id)">삭제</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
/* 애플리케이션 기본 레이아웃 */ 
.todo-container { max-width: 500px; margin: 50px auto; padding: 30px; background: #fff; border-radius: 12px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); font-family: sans-serif; } 
h1 { text-align: center; color: #333; margin-bottom: 25px; } 

/* 입력 폼 영역 스타일 */ 
.input-area { display: flex; gap: 10px; margin-bottom: 25px; } 
.input-area input { flex: 1; padding: 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 16px; outline: none; transition: border-color 0.2s; } 
.input-area input:focus { border-color: #42b883; } 
.input-area button { padding: 12px 20px; background: #42b883; color: white; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; font-size: 15px; transition: background-color 0.2s; } 
.input-area button:hover { background: #33a06f; } 

/* 리스트 및 아이템 스타일 */ 
.todo-list { list-style: none; padding: 0; margin: 0; } 
.todo-item { display: flex; align-items: center; justify-content: space-between; padding: 15px; border-bottom: 1px solid #eee; transition: background-color 0.2s; } 
.todo-item:hover { background: #f9f9f9; } 

/* 체크박스 및 텍스트 상태 스타일 */ 
.checkbox { width: 18px; height: 18px; cursor: pointer; } 
.text { font-size: 16px; color: #333; flex: 1; margin-left: 15px; transition: color 0.2s; } 
.completed { text-decoration: line-through; color: #9e9e9e; } 

/* 삭제 버튼 스타일 */ 
.delete-btn { background: #e53935; color: white; border: none; padding: 8px 12px; border-radius: 6px; cursor: pointer; font-weight: bold; transition: background-color 0.2s; } 
.delete-btn:hover { background: #c62828; } 
</style>