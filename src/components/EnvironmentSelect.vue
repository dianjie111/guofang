<template>
  <div class="environment-select-card">
    <div class="card-header">
      <div class="header-icon">🌍</div>
      <div class="header-text">
        <h3 class="card-title">战场环境选择</h3>
        <p class="card-subtitle">选择不同环境进行通信参数配置</p>
      </div>
    </div>
    <div class="environment-cards">
      <div 
        v-for="env in environments" 
        :key="env.value"
        class="environment-card"
        :class="{ active: selectedEnvironment === env.value }"
        @click="selectedEnvironment = env.value"
      >
        <div class="env-background" :style="{ background: env.gradient }"></div>
        <div class="env-content">
          <div class="environment-icon">{{ env.icon }}</div>
          <div class="environment-name">{{ env.name }}</div>
          <div class="environment-tag">{{ env.tag }}</div>
        </div>
      </div>
    </div>
    <div class="environment-info" v-if="selectedEnvironment">
      <div class="info-card">
        <template v-if="selectedEnvironment === 'mountain'">
          <div class="info-header">
            <span class="info-icon">⛰️</span>
            <h4>山地环境</h4>
          </div>
          <div class="info-item">
            <span class="info-label">特点</span>
            <span class="info-value">地形复杂，遮挡多，通信距离受限</span>
          </div>
          <div class="info-item">
            <span class="info-label">建议</span>
            <span class="info-value">使用较高功率，选择合适频段（如HF）</span>
          </div>
        </template>
        <template v-else-if="selectedEnvironment === 'city'">
          <div class="info-header">
            <span class="info-icon">🏢</span>
            <h4>城市环境</h4>
          </div>
          <div class="info-item">
            <span class="info-label">特点</span>
            <span class="info-value">建筑物密集，信号反射多，干扰源多</span>
          </div>
          <div class="info-item">
            <span class="info-label">建议</span>
            <span class="info-value">使用抗干扰技术，合理选择频率（如UHF）</span>
          </div>
        </template>
        <template v-else-if="selectedEnvironment === 'interference'">
          <div class="info-header">
            <span class="info-icon">⚡</span>
            <h4>强电磁干扰环境</h4>
          </div>
          <div class="info-item">
            <span class="info-label">特点</span>
            <span class="info-value">电磁环境复杂，干扰强度大</span>
          </div>
          <div class="info-item">
            <span class="info-label">建议</span>
            <span class="info-value">使用跳频、扩频等抗干扰技术，提高功率</span>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, defineExpose } from 'vue'

const selectedEnvironment = ref('mountain')

const environments = [
  { 
    value: 'mountain', 
    name: '山地', 
    icon: '🏔️', 
    gradient: 'linear-gradient(135deg, #00ff88 0%, #009950 100%)',
    tag: '远距离通信'
  },
  { 
    value: 'city', 
    name: '城市', 
    icon: '🏙️', 
    gradient: 'linear-gradient(135deg, #00d4ff 0%, #006699 100%)',
    tag: '城市作战'
  },
  { 
    value: 'interference', 
    name: '强电磁干扰', 
    icon: '⚡', 
    gradient: 'linear-gradient(135deg, #ff6b6b 0%, #993333 100%)',
    tag: '电子战环境'
  }
]

defineExpose({
  getEnvironment: () => selectedEnvironment.value,
  setEnvironment: (env) => { selectedEnvironment.value = env }
})
</script>

<style scoped>
.environment-select-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 24px;
}

.header-icon {
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
}

.card-title {
  font-size: 18px;
  font-weight: 700;
  margin: 0 0 4px 0;
}

.card-subtitle {
  font-size: 13px;
  color: #6b7280;
  margin: 0;
}

.environment-cards {
  display: flex;
  gap: 16px;
  margin-bottom: 24px;
}

.environment-card {
  position: relative;
  flex: 1;
  padding: 24px;
  border: 2px solid #1f2937;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  text-align: center;
  overflow: hidden;
}

.environment-card:hover {
  border-color: #374151;
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
}

.environment-card.active {
  border-color: #00ff88;
  box-shadow: 0 0 20px rgba(0, 255, 136, 0.2);
}

.env-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.environment-card.active .env-background {
  opacity: 0.1;
}

.env-content {
  position: relative;
  z-index: 1;
}

.environment-icon {
  font-size: 40px;
  margin-bottom: 12px;
}

.environment-name {
  font-size: 15px;
  font-weight: 600;
  margin-bottom: 8px;
}

.environment-tag {
  display: inline-block;
  padding: 4px 12px;
  background: rgba(0, 255, 136, 0.1);
  border-radius: 50px;
  font-size: 11px;
  color: #00ff88;
  letter-spacing: 1px;
  font-weight: 600;
}

.environment-info {
  margin-top: 24px;
}

.info-card {
  background: rgba(31, 41, 55, 0.5);
  border: 1px solid #1f2937;
  border-radius: 12px;
  padding: 20px;
}

.info-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
  padding-bottom: 12px;
  border-bottom: 1px solid #1f2937;
}

.info-icon {
  font-size: 24px;
}

.info-header h4 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
}

.info-item {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 10px 0;
}

.info-label {
  font-size: 12px;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
  min-width: 50px;
}

.info-value {
  font-size: 14px;
  color: #d1d5db;
  line-height: 1.5;
}
</style>