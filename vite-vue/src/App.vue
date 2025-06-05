<template>
  <div id="app">
    <div class="app-container">
      <h1 class="title">时光沙漏</h1>
      <div class="mode-toggle">
        <button 
          :class="['toggle-btn', { active: mode === 'timer' }]" 
          @click="mode = 'timer'"
        >
          计时器
        </button>
        <button 
          :class="['toggle-btn', { active: mode === 'countdown' }]" 
          @click="mode = 'countdown'"
        >
          倒计时
        </button>
      </div>
      <Timer v-if="mode === 'timer'" />
      <CountdownTimer v-else />
      <div class="particle-layer"></div>
    </div>
    <div class="nebula-overlay"></div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Timer from './components/Timer.vue'
import CountdownTimer from './components/CountdownTimer.vue'

const mode = ref('timer') // 'timer' 或 'countdown'
</script>

<style>
/* 全局复位 & 让 #app 真实占满可视区 */
html, body, #app {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  overflow: hidden;
  font-family: 'Poppins', 'Segoe UI', sans-serif;
  background: 
    radial-gradient(circle at 30% 40%, rgba(76, 0, 130, 0.8), transparent 40%),
    radial-gradient(circle at 70% 20%, rgba(139, 0, 139, 0.7), transparent 35%),
    radial-gradient(circle at 50% 80%, rgba(25, 25, 112, 0.9), transparent 45%),
    linear-gradient(135deg, #0f0c29, #302b63, #24243e);
  background-size: cover;
  background-attachment: fixed;
  background-repeat: no-repeat;
  background-position: center center;
}

#app {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  width: 100%;
}

.nebula-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: 
    radial-gradient(circle, rgba(255,255,255,0.04) 0%, transparent 80%),
    radial-gradient(circle, rgba(255,255,255,0.02) 0%, transparent 80%),
    radial-gradient(circle, rgba(255,255,255,0.03) 0%, transparent 80%);
  opacity: 0.7;
  z-index: 0;
}

/* 中央卡片容器：玻璃拟态风格 */
.app-container {
  position: relative;
  width: 90%;
  max-width: 640px;
  padding: 40px 30px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 30px;
  box-shadow: 
    0 16px 42px rgba(0, 0, 0, 0.45),
    inset 0 0 0 1px rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  text-align: center;
  z-index: 2;
  border-top: 1px solid rgba(255,255,255,0.15);
  border-left: 1px solid rgba(255,255,255,0.12);
}

/* 模式切换按钮组 */
.mode-toggle {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 30px;
}

.toggle-btn {
  padding: 10px 25px;
  border: none;
  border-radius: 12px;
  background: rgba(75, 0, 130, 0.3);
  color: rgba(224, 176, 255, 0.7);
  font-family: 'Exo 2', sans-serif;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  backdrop-filter: blur(5px);
  position: relative;
  overflow: hidden;
}

.toggle-btn:hover {
  background: rgba(138, 43, 226, 0.4);
}

.toggle-btn.active {
  background: rgba(138, 43, 226, 0.6);
  color: white;
  box-shadow: 0 0 15px rgba(138, 43, 226, 0.5);
}

.toggle-btn.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: rgba(224, 176, 255, 0.8);
  border-radius: 0 0 10px 10px;
}

/* 粒子层效果 */
.particle-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    radial-gradient(white 1px, transparent 1px),
    radial-gradient(white 1px, transparent 1px);
  background-size: 50px 50px;
  background-position: 0 0, 25px 25px;
  opacity: 0.03;
  z-index: -1;
  animation: particleMove 120s linear infinite;
}

@keyframes particleMove {
  100% {
    background-position: 500px 500px, 525px 525px;
  }
}

/* 页面主标题 */
.title {
  font-family: 'Bebas Neue', 'Segoe UI', sans-serif;
  font-size: 3.5rem;
  color: rgba(255,255,255,0.95);
  margin-bottom: 25px;
  letter-spacing: 3px;
  text-shadow: 
    0 0 10px rgba(224, 176, 255, 0.4),
    0 0 20px rgba(138, 43, 226, 0.3),
    0 0 30px rgba(75, 0, 130, 0.2);
  position: relative;
  display: inline-block;
}

.title::after {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 10%;
  width: 80%;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(224, 176, 255, 0.7), transparent);
  border-radius: 50%;
  filter: blur(1px);
}

/* 响应式：在超小屏幕时缩减卡片 & 标题 */
@media (max-width: 480px) {
  .app-container {
    padding: 25px 20px;
    border-radius: 20px;
  }
  
  .title {
    font-size: 2.5rem;
    margin-bottom: 15px;
  }
  
  .toggle-btn {
    padding: 8px 20px;
    font-size: 0.9rem;
  }
}
</style>