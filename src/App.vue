<template>
  <div class="app">
    <header class="header">
      <h1 class="title">KPT00L // NFC</h1>
      <p class="subtitle">READ | WRITE | KEYS | PROFILES</p>
    </header>

    <main class="main-content">
      <!-- NFC/Offline Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">NFC//OFFLINE</h2>
          <p class="card-description">等待授权</p>
        </div>
        <div class="card-body">
          <button class="btn enable-btn">ENABLE</button>
        </div>
      </div>

      <!-- Read/Dump Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">READ//DUMP</h2>
          <p class="card-description">读取 NFC 标签数据（房区/块）</p>
        </div>
        <div class="card-body">
          <button class="btn">Read NFC</button>
        </div>
      </div>

      <!-- Write/Flash Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">WRITE//FLASH</h2>
          <p class="card-description">写入数据到 NFC 标签（支持配置库）</p>
        </div>
        <div class="card-body">
          <button class="btn">Write NFC</button>
        </div>
      </div>

      <!-- Settings/Keys Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">SETTINGS//KEYS</h2>
          <p class="card-description">管理密钥集（KeyA/KeyB）</p>
        </div>
        <div class="card-body">
          <button class="btn">Manage Keys</button>
        </div>
      </div>

      <!-- About/Author Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">ABOUT//AUTHOR</h2>
          <p class="card-description">关于作者 / 项目信息</p>
        </div>
        <div class="card-body">
          <button class="btn">About</button>
        </div>
      </div>

      <!-- Test/Toast Card -->
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">TEST//TOAST</h2>
          <p class="card-description">点击弹出 Toast</p>
        </div>
        <div class="card-body">
          <button class="btn">Test Toast</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const enableNfc = ref(false);

const toggleNfc = () => {
  enableNfc.value = !enableNfc.value;
};
</script>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
  background-color: #121212; /* Dark background */
  font-family: 'Arial', sans-serif;
  color: #d6ffe2;
}

/* Header */
.header {
  text-align: center;
  margin-top: 20px;
}

.title {
  font-size: 2em;
  color: #22c55e; /* Green for header */
}

.subtitle {
  color: #a3b7b1; /* Muted green */
  font-size: 1.2em;
}

/* Main Content Layout */
.main-content {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  padding: 20px;
  width: 100%;
  max-width: 900px;
}

/* Card Styles */
.card {
  background-color: #1e1e1e;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.5);
  overflow: hidden;
  transition: transform 0.3s ease-in-out;
}

.card:hover {
  transform: translateY(-5px);
}

.card-header {
  background-color: #0077b6; /* Blue header background */
  padding: 20px;
  color: #fff;
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

.card-btn {
  background-color: #22c55e; /* Green button */
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 10px;
  font-size: 1.1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.card-btn:hover {
  background-color: #16a34a;
}

/* Enable Button Styling */
.enable-btn {
  background-color: #ff6f61; /* Red for enable button */
  color: white;
}

.enable-btn:hover {
  background-color: #e55347;
}

/* Mobile responsiveness */
@media (max-width: 768px) {
  .main-content {
    grid-template-columns: 1fr; /* Single column layout */
  }

  .card {
    margin: 0 10px; /* Add margin on mobile */
  }
}
</style>