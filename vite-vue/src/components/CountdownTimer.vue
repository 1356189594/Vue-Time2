<template>
  <div class="countdown-wrapper">
    <!-- 时间输入区域 -->
    <div class="time-inputs">
      <div class="input-group">
        <label for="hours">小时</label>
        <input 
          type="number" 
          id="hours" 
          v-model.number="hours" 
          min="0" 
          max="23"
          :disabled="running"
        >
      </div>
      
      <div class="input-group">
        <label for="minutes">分钟</label>
        <input 
          type="number" 
          id="minutes" 
          v-model.number="minutes" 
          min="0" 
          max="59"
          :disabled="running"
        >
      </div>
      
      <div class="input-group">
        <label for="seconds">秒钟</label>
        <input 
          type="number" 
          id="seconds" 
          v-model.number="seconds" 
          min="0" 
          max="59"
          :disabled="running"
        >
      </div>
    </div>
    
    <!-- 倒计时显示区域 -->
    <div class="countdown-display">
      <div class="digit">{{ formattedHours }}</div>
      <div class="colon">:</div>
      <div class="digit">{{ formattedMinutes }}</div>
      <div class="colon">:</div>
      <div class="digit">{{ formattedSeconds }}</div>
    </div>
    
    <!-- 操作按钮组 -->
    <div class="countdown-buttons">
      <button 
        class="btn start" 
        @click="startCountdown"
        :disabled="running || totalSeconds <= 0"
      >
        <div class="btn-inner">
          <span class="btn-text">{{ running ? '继续' : '开始' }}</span>
          <div class="btn-glow"></div>
        </div>
      </button>
      
      <button 
        class="btn pause" 
        @click="pauseCountdown" 
        :disabled="!running || isFinished"
      >
        <div class="btn-inner">
          <span class="btn-text">暂停</span>
          <div class="btn-glow"></div>
        </div>
      </button>
      
      <button class="btn reset" @click="resetCountdown">
        <div class="btn-inner">
          <span class="btn-text">重置</span>
          <div class="btn-glow"></div>
        </div>
      </button>
    </div>
    
    <!-- 进度条 -->
    <div class="timeline">
      <div class="progress" :style="{ width: progressWidth }"></div>
    </div>
    
    <!-- 结束提示 -->
    <div v-if="isFinished" class="finish-alert">
      <div class="alert-content">
        <div class="alert-icon">!</div>
        <div class="alert-text">倒计时结束！</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from 'vue'

// 时间输入
const hours = ref(0)
const minutes = ref(0)
const seconds = ref(0)

// 状态管理
const running = ref(false)
const countdownSeconds = ref(0)
const isFinished = ref(false)
let countdownTimer = null
let finishTimer = null // 用于控制结束提示的定时器

// 计算总秒数
const totalSeconds = computed(() => {
  return hours.value * 3600 + minutes.value * 60 + seconds.value
})

// 格式化显示
const formattedHours = computed(() => {
  return Math.floor(countdownSeconds.value / 3600).toString().padStart(2, '0')
})

const formattedMinutes = computed(() => {
  return Math.floor((countdownSeconds.value % 3600) / 60).toString().padStart(2, '0')
})

const formattedSeconds = computed(() => {
  return (countdownSeconds.value % 60).toString().padStart(2, '0')
})

// 进度条
const progressWidth = computed(() => {
  return totalSeconds.value > 0 
    ? `${(1 - countdownSeconds.value / totalSeconds.value) * 100}%` 
    : '0%'
})

// 开始倒计时
function startCountdown() {
  if (running.value || totalSeconds.value <= 0) return
  
  if (!isFinished.value && countdownSeconds.value === 0) {
    countdownSeconds.value = totalSeconds.value
  }
  
  running.value = true
  isFinished.value = false
  clearTimeout(finishTimer) // 清除可能存在的结束提示定时器
  
  countdownTimer = setInterval(() => {
    if (countdownSeconds.value > 0) {
      countdownSeconds.value--
    } else {
      finishCountdown()
    }
  }, 1000)
}

// 暂停倒计时
function pauseCountdown() {
  if (!running.value) return
  clearInterval(countdownTimer)
  running.value = false
}

// 重置倒计时
function resetCountdown() {
  clearInterval(countdownTimer)
  clearTimeout(finishTimer) // 清除结束提示定时器
  countdownSeconds.value = totalSeconds.value
  running.value = false
  isFinished.value = false
}

// 结束倒计时
function finishCountdown() {
  clearInterval(countdownTimer)
  running.value = false
  isFinished.value = true
  playFinishSound()
  
  // 5秒后自动隐藏结束提示
  finishTimer = setTimeout(() => {
    isFinished.value = false
  }, 5000)
}

// 播放结束音效
function playFinishSound() {
  const context = new (window.AudioContext || window.webkitAudioContext)()
  const oscillator = context.createOscillator()
  const gainNode = context.createGain()
  
  oscillator.connect(gainNode)
  gainNode.connect(context.destination)
  
  // 设置音调
  oscillator.type = 'sine'
  oscillator.frequency.setValueAtTime(440, context.currentTime)
  
  // 设置音量变化
  gainNode.gain.setValueAtTime(0.3, context.currentTime)
  gainNode.gain.exponentialRampToValueAtTime(0.01, context.currentTime + 1.5)
  
  // 播放音效
  oscillator.start(context.currentTime)
  oscillator.stop(context.currentTime + 1.5)
}

// 监听输入变化
watch([hours, minutes, seconds], () => {
  // 确保输入值在合理范围内
  if (hours.value < 0) hours.value = 0
  if (minutes.value < 0) minutes.value = 0
  if (seconds.value < 0) seconds.value = 0
  
  if (minutes.value > 59) minutes.value = 59
  if (seconds.value > 59) seconds.value = 59
  
  // 重置倒计时
  countdownSeconds.value = totalSeconds.value
})

// 组件卸载时清除定时器
onUnmounted(() => {
  clearInterval(countdownTimer)
  clearTimeout(finishTimer)
})
</script>

<style scoped>
.countdown-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
  width: 100%;
  position: relative;
}

.time-inputs {
  display: flex;
  gap: 20px;
  margin-bottom: 10px;
  justify-content: center; /* 确保输入区域居中 */
}

.input-group {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.input-group label {
  font-family: 'Exo 2', sans-serif;
  color: rgba(200, 200, 255, 0.8);
  margin-bottom: 8px;
  font-size: 0.9rem;
}

.input-group input {
  width: 70px;
  height: 40px;
  text-align: center;
  font-family: 'Orbitron', monospace;
  font-size: 1.5rem;
  color: white;
  background: rgba(75, 0, 130, 0.4);
  border: 1px solid rgba(138, 43, 226, 0.5);
  border-radius: 8px;
  outline: none;
  padding: 5px;
  box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.3);
  transition: all 0.3s ease;
}

.input-group input:focus {
  border-color: rgba(224, 176, 255, 0.8);
  box-shadow: 0 0 15px rgba(138, 43, 226, 0.5);
}

.input-group input:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* 倒计时显示 - 确保居中 */
.countdown-display {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Orbitron', 'Courier New', monospace;
  margin-bottom: 15px;
  width: 100%; /* 确保宽度100% */
}

.digit {
  font-size: 4rem;
  font-weight: 700;
  color: white;
  text-shadow: 0 0 12px rgba(224, 176, 255, 0.7);
  min-width: 80px; /* 确保每个数字区域宽度一致 */
  text-align: center;
  background: rgba(75, 0, 130, 0.5);
  border-radius: 10px;
  padding: 10px;
  margin: 0 5px;
  box-shadow: 
    0 5px 15px rgba(0, 0, 0, 0.3),
    inset 0 0 10px rgba(255, 255, 255, 0.1);
  transition: background-color 0.3s ease;
}

.digit.low-time {
  background: rgba(139, 0, 0, 0.5);
  animation: pulse 1s infinite;
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

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}

/* 按钮组 - 确保居中 */
.countdown-buttons {
  display: flex;
  gap: 25px;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 10px;
  width: 100%; /* 确保宽度100% */
}

/* 按钮样式 */
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
    inset 0 极客  0 15px rgba(255, 255, 255, 0.3),
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

/* 结束提示 */
.finish-alert {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(15, 12, 41, 0.85);
  border-radius: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.alert-content {
  background: rgba(75, 0, 130, 0.9);
  border-radius: 20px;
  padding: 40px;
  text-align: center;
  box-shadow: 0 0 40px rgba(224, 176, 255, 0.6);
  border: 1px solid rgba(224, 176, 255, 0.4);
  position: relative;
  overflow: hidden;
}

.alert-content::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: conic-gradient(
    transparent,
    rgba(224, 176, 255, 0.6),
    transparent
  );
  animation: rotate 3s linear infinite;
  z-index: -1;
}

@keyframes rotate {
  to { transform: rotate(360deg); }
}

.alert-icon {
  font-size: 5rem;
  color: white;
  width: 100px;
  height: 100px;
  background: rgba(224, 176, 255, 0.2);
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto 20px;
  text-shadow: 0 0 20px rgba(255, 0, 0, 0.7);
  animation: pulse 1.5s infinite;
}

.alert-text {
  font-family: 'Orbitron', sans-serif;
  font-size: 2.5rem;
  color: white;
  letter-spacing: 2px;
  text-shadow: 0 0 15px rgba(224, 176, 255, 0.8);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .time-inputs {
    gap: 15px;
  }
  
  .input-group input {
    width: 60px;
    height: 35px;
    font-size: 1.3rem;
  }
  
  .digit {
    font-size: 3.2rem;
    min-width: 70px;
    padding: 8px;
  }
  
  .colon {
    font-size: 3.2rem;
  }
  
  .btn {
    width: 100px;
    height: 45px;
  }
  
  .btn-text {
    font-size: 1.1rem;
  }
  
  .alert-icon {
    font-size: 4rem;
    width: 80px;
    height: 80px;
  }
  
  .alert-text {
    font-size: 2rem;
  }
}

@media (max-width: 480px) {
  .time-inputs {
    gap: 10px;
  }
  
  .input-group {
    min-width: 65px;
  }
  
  .input-group input {
    width: 50px;
    height: 30px;
    font-size: 1.1rem;
  }
  
  .digit {
    font-size: 2.5rem;
    min-width: 60px;
    padding: 6px;
  }
  
  .colon {
    font-size: 2.5rem;
  }
  
  .countdown-buttons {
    gap: 15px;
  }
  
  .btn {
    width: 85px;
    height: 40px;
  }
  
  .btn-text {
    font-size: 0.95rem;
    letter-spacing: 1px;
  }
  
  .alert-icon {
    font-size: 3rem;
    width: 60px;
    height: 60px;
  }
  
  .alert-text {
    font-size: 1.5rem;
  }
}
</style>