<template>
  <div class="app">
    <header class="header">
      <h1 class="title">Vue3 Card Layout</h1>
      <p class="subtitle">仿Kotlin Compose卡片布局</p>
    </header>

    <main class="main-content">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Card 1</h2>
          <p class="card-description">This is a simple card that holds content.</p>
        </div>
        <div class="card-body">
          <p class="card-text">Here’s some text inside the card. You can add images, buttons, or any content.</p>
        </div>
        <div class="card-footer">
          <button class="card-btn">Action</button>
        </div>
      </div>

      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Card 2</h2>
          <p class="card-description">Another card with different content.</p>
        </div>
        <div class="card-body">
          <p class="card-text">Add whatever you like to make the card more interactive!</p>
        </div>
        <div class="card-footer">
          <button class="card-btn">Action</button>
        </div>
      </div>

      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Card 3</h2>
          <p class="card-description">This card could be used for additional content.</p>
        </div>
        <div class="card-body">
          <p class="card-text">Each card can have its own unique content and style.</p>
        </div>
        <div class="card-footer">
          <button class="card-btn">Action</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from "vue";

// You can define more logic here if needed
</script>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
  background-color: #f4f4f4;
  font-family: 'Arial', sans-serif;
}

.header {
  text-align: center;
  margin-top: 20px;
}

.title {
  font-size: 2em;
  color: #333;
}

.subtitle {
  color: #666;
  font-size: 1.2em;
}

.main-content {
  display: grid;
  grid-template-columns: 1fr; /* 默认单列 */
  gap: 20px;
  padding: 20px;
  width: 100%;
  max-width: 1000px; /* 最大宽度限制 */
}

.card {
  background-color: white;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: transform 0.3s ease-in-out;
}

.card:hover {
  transform: translateY(-5px);
}

.card-header {
  background-color: #3498db;
  padding: 20px;
  color: white;
  text-align: center;
}

.card-title {
  font-size: 1.5em;
  margin: 0;
}

.card-description {
  font-size: 1em;
  opacity: 0.8;
}

.card-body {
  padding: 20px;
}

.card-text {
  font-size: 1.1em;
  color: #333;
}

.card-footer {
  text-align: center;
  padding: 15px;
  background-color: #f0f0f0;
}

.card-btn {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1.1em;
  transition: background-color 0.3s;
}

.card-btn:hover {
  background-color: #2980b9;
}

/* 移动端优化 */
@media (max-width: 768px) {
  .main-content {
    grid-template-columns: 1fr; /* 在小屏幕上保持单列布局 */
  }

  .card {
    margin: 0 10px; /* 为卡片添加边距，避免贴边显示 */
  }

  .card-header {
    font-size: 1.2em; /* 调整标题大小 */
  }

  .card-body {
    padding: 15px; /* 在小屏幕上减少卡片内部的填充 */
  }

  .card-btn {
    padding: 8px 16px; /* 按钮在小屏幕上稍微缩小 */
  }
}

/* 平板及更大屏幕设备优化 */
@media (min-width: 769px) {
  .main-content {
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); /* 自适应卡片布局 */
  }
}
</style>