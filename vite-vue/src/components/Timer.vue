<template>
  <div class="timer-wrapper">
    <!-- 计时显示区：确保格式为 HH:MM:SS -->
    <div class="timer-display">
      <div class="digit">{{ formattedHours }}</div>
      <div class="colon">:</div>
      <div class="digit">{{ formattedMinutes }}</div>
      <div class="colon">:</div>
      <div class="digit">{{ formattedSeconds }}</div>
    </div>

    <!-- 操作按钮组 -->
    <div class="timer-buttons">
      <button class="btn start" @click="startTimer" :disabled="running">
        <div class="btn-inner">
          <span class="btn-text">开始</span>
          <div class="btn-glow"></div>
        </div>
      </button>
      <button class="btn pause" @click="pauseTimer" :disabled="!running">
        <div class="btn-inner">
          <span class="btn-text">暂停</span>
          <div class="btn-glow"></div>
        </div>
      </button>
      <button class="btn reset" @click="resetTimer">
        <div class="btn-inner">
          <span class="btn-text">重置</span>
          <div class="btn-glow"></div>
        </div>
      </button>
    </div>
    
    <div class="timeline">
      <div class="progress" :style="{ width: progressWidth }"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted, watch } from 'vue'

// 响应式秒数和运行标记
const seconds = ref(0)
const running = ref(false)
let timerId = null

// 格式化时间函数，补零到两位数
const formattedHours = computed(() => {
  return Math.floor(seconds.value / 3600).toString().padStart(2, '0')
})

const formattedMinutes = computed(() => {
  return Math.floor((seconds.value % 3600) / 60).toString().padStart(2, '0')
})

const formattedSeconds = computed(() => {
  return (seconds.value % 60).toString().padStart(2, '0')
})

// 启动计时
function startTimer() {
  if (running.value) return
  running.value = true
  timerId = setInterval(() => {
    seconds.value++
  }, 1000)
}

// 暂停计时
function pauseTimer() {
  clearInterval(timerId)
  running.value = false
}

// 重置计时
function resetTimer() {
  clearInterval(timerId)
  seconds.value = 0
  running.value = false
}

// 组件卸载时清除定时器
onUnmounted(() => {
  clearInterval(timerId)
})

// 进度条逻辑
const progressWidth = ref('0%')
watch(seconds, (newVal) => {
  progressWidth.value = `${(newVal % 60) / 60 * 100}%`
})
</script>

<style scoped>
/* 根容器：垂直布局，居中对齐 */
.timer-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 35px;
  width: 100%;
  position: relative;
}

/* 计时文本：优化显示格式 */
.timer-display {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Orbitron', 'Courier New', monospace;
  width: 100%; /* 新增：确保宽度100% */
}

.digit {
  font-size: 4rem;
  font-weight: 700;
  color: white;
  text-shadow: 0 0 12px rgba(224, 176, 255, 0.7);
  min-width: 80px; /* 保证每个数字区域宽度一致 */
  text-align: center;
  background: rgba(75, 0, 130, 0.5);
  border-radius: 10px;
  padding: 10px;
  margin: 0 5px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.colon {
  font-size: 4rem;
  color: rgba(224, 176, 255, 0.7);
  margin: 0 5px;
  animation: blink 1.5s infinite;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

/* 按钮组：水平等距排列 */
.timer-buttons {
  display: flex;
  gap: 25px;
  flex-wrap: wrap;
  justify-content: center;
  width: 100%; /* 新增：确保宽度100% */
}

/* 玻璃拟态按钮基础样式 */
.btn {
  position: relative;
  padding: 0;
  border: none;
  border-radius: 15px;
  background: transparent;
  cursor: pointer;
  transition: transform 0.3s ease;
  width: 120px;
  height: 50px;
  perspective: 500px;
}

.btn-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  transform: translateZ(25px);
}

.btn:hover:not(:disabled) .btn-inner {
  transform: translateZ(30px);
}

.btn:active:not(:disabled) .btn-inner {
  transform: translateZ(15px);
  transition: transform 0.1s ease;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-text {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  font-family: 'Exo 2', 'Segoe UI', Tahoma, sans-serif;
  font-size: 1.2rem;
  font-weight: 600;
  letter-spacing: 1.5px;
  color: #ffffff;
  z-index: 2;
  border-radius: 15px;
  transform: translateZ(25px);
}

.btn-glow {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 15px;
  filter: blur(12px);
  opacity: 0.7;
  transition: opacity 0.3s ease;
  z-index: 1;
}

.start .btn-text {
  background: rgba(75, 0, 130, 0.7);
  box-shadow: 
    inset 0 0 15px rgba(255, 255, 255, 0.3),
    0 5px 20px rgba(138, 43, 226, 0.5);
  border: 1px solid rgba(224, 176, 255, 0.4);
}

.start .btn-glow {
  background: radial-gradient(circle at center, rgba(138, 43, 226, 0.8), transparent 70%);
}

.pause .btn-text {
  background: rgba(139, 0, 139, 0.7);
  box-shadow: 
    inset 0 0 15px rgba(255, 255, 255, 0.3),
    0 5px 20px rgba(255, 20, 147, 0.5);
  border: 1px solid rgba(255, 182, 193, 0.4);
}

.pause .btn-glow {
  background: radial-gradient(circle at center, rgba(255, 20, 147, 0.8), transparent 70%);
}

.reset .btn-text {
  background: rgba(25, 25, 112, 0.7);
  box-shadow: 
    inset 0 0 15px rgba(255, 255, 255, 0.3),
    0 5px 20px rgba(65, 105, 225, 0.5);
  border: 1px solid rgba(135, 206, 250, 0.4);
}

.reset .btn-glow {
  background: radial-gradient(circle at center, rgba(65, 105, 225, 0.8), transparent 70%);
}

.timeline {
  position: absolute;
  bottom: -20px;
  left: 0;
  width: 100%;
  height: 4px;
  background: rgba(138, 43, 226, 0.2);
  border-radius: 2px;
  overflow: hidden;
}

.progress {
  height: 100%;
  background: linear-gradient(90deg, 
    transparent, 
    rgba(224, 176, 255, 0.8),
    rgba(138, 43, 226, 0.8),
    transparent);
  transition: width 0.5s ease;
  box-shadow: 0 0 10px rgba(224, 176, 255, 0.6);
}

/* 响应式：小屏幕时缩小文字和按钮 */
@media (max-width: 480px) {
  .digit {
    font-size: 2.5rem;
    min-width: 60px;
    padding: 8px;
  }
  
  .colon {
    font-size: 2.5rem;
  }
  
  .btn {
    width: 100px;
    height: 45px;
  }
  
  .btn-text {
    font-size: 1rem;
  }
  
  .timer-buttons {
    gap: 15px;
  }
}
</style>