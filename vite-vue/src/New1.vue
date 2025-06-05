<template>
  <div class="new1-container">
    <div class="galaxy-card">
      <div class="holographic-bar"></div>
      <div class="card-content">
        <h2>维度探索</h2>
        <p class="subtitle">穿越时空的旅程</p>
        
        <div class="interactive-element">
          <div class="energy-sphere" @click="increment">
            <div class="inner-sphere">
              <div class="count-display">{{ count }}</div>
              <div class="dimension-label">维度能量</div>
            </div>
            <div class="particle-ring"></div>
            <div class="particle particle-1"></div>
            <div class="particle particle-2"></div>
            <div class="particle particle-3"></div>
          </div>
          <p class="hint">点击能量球增加维度能量</p>
        </div>
      </div>
      
      <div class="digital-grid"></div>
      
      <div class="dimension-info">
        <div class="info-box">
          <div class="info-label">当前维度</div>
          <div class="info-value">第{{ count % 10 + 1 }}维度</div>
        </div>
        <div class="info-box">
          <div class="info-label">空间稳定性</div>
          <div class="info-value">{{ 100 - count % 15 }}%</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
  
  // 添加粒子效果
  const particles = document.querySelectorAll('.particle');
  particles.forEach(p => {
    p.style.animation = 'none';
    setTimeout(() => {
      p.style.animation = 'particleExplode 0.8s ease-out forwards';
    }, 10);
  });
}
</script>

<style scoped>
.new1-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
  background: transparent;
  perspective: 1000px;
}

.galaxy-card {
  position: relative;
  width: 100%;
  max-width: 720px;
  background: rgba(15, 12, 41, 0.7);
  border-radius: 24px;
  overflow: hidden;
  box-shadow: 
    0 25px 70px rgba(0, 0, 0, 0.7),
    inset 0 0 0 1px rgba(138, 43, 226, 0.4);
  backdrop-filter: blur(15px);
  -webkit-backdrop-filter: blur(15px);
  border: 1px solid rgba(138, 43, 226, 0.3);
  transform: perspective(800px) rotateX(2deg);
  transition: transform 0.6s cubic-bezier(0.23, 1, 0.32, 1);
}

.galaxy-card:hover {
  transform: perspective(800px) rotateX(1deg) scale(1.02);
}

.holographic-bar {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 6px;
  background: linear-gradient(90deg, 
    transparent, 
    rgba(138, 43, 226, 0.9),
    rgba(75, 0, 130, 0.9),
    rgba(138, 43, 226, 0.9),
    transparent);
  filter: blur(3px);
  animation: hologramFlow 3s linear infinite;
}

@keyframes hologramFlow {
  0% { background-position: -100% 0; }
  100% { background-position: 200% 0; }
}

.card-content {
  padding: 50px 40px;
  text-align: center;
  position: relative;
  z-index: 2;
}

h2 {
  font-family: 'Orbitron', 'Arial', sans-serif;
  font-size: 3.2rem;
  color: rgba(224, 176, 255, 0.95);
  margin-bottom: 15px;
  text-shadow: 0 0 15px rgba(224, 176, 255, 0.6);
  letter-spacing: 2px;
  position: relative;
  display: inline-block;
}

h2::after {
  content: '';
  position: absolute;
  bottom: -8px;
  left: 15%;
  width: 70%;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(224, 176, 255, 0.7), transparent);
  border-radius: 50%;
}

.subtitle {
  font-family: 'Exo 2', 'Arial', sans-serif;
  font-size: 1.5rem;
  color: rgba(200, 200, 255, 0.8);
  margin-bottom: 40px;
  letter-spacing: 1px;
  text-shadow: 0 0 10px rgba(224, 176, 255, 0.3);
}

.interactive-element {
  margin: 50px 0;
}

.energy-sphere {
  position: relative;
  width: 220px;
  height: 220px;
  margin: 0 auto;
  cursor: pointer;
  transition: transform 0.4s ease;
}

.energy-sphere:hover {
  transform: scale(1.05);
}

.energy-sphere:active {
  transform: scale(0.95);
}

.inner-sphere {
  position: absolute;
  top: 10%;
  left: 10%;
  width: 80%;
  height: 80%;
  background: radial-gradient(
    circle at 30% 30%,
    rgba(138, 43, 226, 0.8),
    rgba(75, 0, 130, 0.9)
  );
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow: 
    inset 0 0 30px rgba(255, 255, 255, 0.3),
    0 0 60px rgba(138, 43, 226, 0.8);
  z-index: 2;
}

.count-display {
  font-family: 'Orbitron', sans-serif;
  font-size: 4.5rem;
  font-weight: 800;
  color: white;
  text-shadow: 0 0 15px rgba(255, 255, 255, 0.9);
  margin-bottom: 5px;
}

.dimension-label {
  font-family: 'Exo 2', sans-serif;
  color: rgba(230, 230, 255, 0.8);
  font-size: 1.2rem;
  letter-spacing: 1px;
}

.particle-ring {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: 
    radial-gradient(circle, transparent 65%, rgba(224, 176, 255, 0.4) 80%, transparent 95%);
  animation: particleOrbit 4s linear infinite;
  z-index: 1;
}

@keyframes particleOrbit {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.particle {
  position: absolute;
  border-radius: 50%;
  background: rgba(224, 176, 255, 0.8);
  box-shadow: 0 0 15px rgba(224, 176, 255, 0.7);
  z-index: 3;
  opacity: 0;
}

.particle-1 {
  top: 10%;
  left: 50%;
  width: 8px;
  height: 8px;
}

.particle-2 {
  top: 70%;
  left: 20%;
  width: 12px;
  height: 12px;
}

.particle-3 {
  top: 40%;
  left: 80%;
  width: 10px;
  height: 10px;
}

@keyframes particleExplode {
  0% {
    transform: translate(0, 0) scale(0.5);
    opacity: 1;
  }
  100% {
    transform: translate(
      calc(var(--tx) * 100px), 
      calc(var(--ty) * 100px)
    ) scale(0);
    opacity: 0;
  }
}

.hint {
  margin-top: 30px;
  font-family: 'Exo 2', sans-serif;
  color: rgba(200, 200, 255, 0.7);
  font-size: 1.3rem;
  letter-spacing: 0.8px;
}

.digital-grid {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    linear-gradient(rgba(138, 43, 226, 0.08) 1px, transparent 1px),
    linear-gradient(90deg, rgba(138, 43, 226, 0.08) 1px, transparent 1px);
  background-size: 25px 25px;
  opacity: 0.3;
  z-index: 1;
}

.dimension-info {
  display: flex;
  justify-content: center;
  gap: 40px;
  margin-top: 20px;
  padding: 0 20px 40px;
}

.info-box {
  background: rgba(25, 25, 112, 0.3);
  border-radius: 15px;
  padding: 20px 30px;
  min-width: 180px;
  border: 1px solid rgba(138, 43, 226, 0.4);
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
  text-align: center;
}

.info-label {
  font-family: 'Exo 2', sans-serif;
  color: rgba(200, 200, 255, 0.7);
  font-size: 1.1rem;
  margin-bottom: 10px;
}

.info-value {
  font-family: 'Orbitron', sans-serif;
  color: rgba(224, 176, 255, 0.95);
  font-size: 1.8rem;
  font-weight: 600;
  text-shadow: 0 0 10px rgba(224, 176, 255, 0.4);
}

/* 响应式调整 */
@media (max-width: 768px) {
  .galaxy-card {
    max-width: 580px;
  }
  
  h2 {
    font-size: 2.5rem;
  }
  
  .subtitle {
    font-size: 1.3rem;
  }
  
  .energy-sphere {
    width: 200px;
    height: 200px;
  }
  
  .count-display {
    font-size: 3.8rem;
  }
  
  .dimension-info {
    flex-direction: column;
    align-items: center;
    gap: 20px;
  }
  
  .info-box {
    min-width: 160px;
    padding: 15px 25px;
  }
}

@media (max-width: 480px) {
  .galaxy-card {
    max-width: 400px;
  }
  
  .card-content {
    padding: 30px 20px;
  }
  
  h2 {
    font-size: 2rem;
  }
  
  .subtitle {
    font-size: 1.1rem;
    margin-bottom: 30px;
  }
  
  .energy-sphere {
    width: 180px;
    height: 180px;
  }
  
  .count-display {
    font-size: 3.2rem;
  }
  
  .dimension-label {
    font-size: 1rem;
  }
  
  .hint {
    font-size: 1.1rem;
    margin-top: 20px;
  }
  
  .dimension-info {
    gap: 15px;
    padding-bottom: 30px;
  }
  
  .info-box {
    min-width: 140px;
    padding: 12px 20px;
  }
  
  .info-value {
    font-size: 1.5rem;
  }
}
</style>