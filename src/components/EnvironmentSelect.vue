<template>
  <el-card class="environment-select-card" :body-style="{ padding: '24px' }">
    <template #header>
      <div class="card-header">
        <div class="header-icon">🌍</div>
        <div class="header-text">
          <h3 class="card-title">环境选择</h3>
          <p class="card-subtitle">选择通信环境以获得最佳配置</p>
        </div>
      </div>
    </template>
    <div class="environment-grid">
      <el-radio-group v-model="selectedEnvironment" class="environment-options">
        <el-radio
          v-for="env in environments"
          :key="env.value"
          :label="env.value"
          class="environment-card"
        >
          <div class="environment-content">
            <div class="environment-icon">{{ env.icon }}</div>
            <div class="environment-info">
              <h4 class="environment-name">{{ env.name }}</h4>
              <p class="environment-desc">{{ env.description }}</p>
              <div class="environment-challenges">
                <span 
                  v-for="challenge in env.challenges" 
                  :key="challenge"
                  class="challenge-tag"
                >
                  {{ challenge }}
                </span>
              </div>
            </div>
          </div>
        </el-radio>
      </el-radio-group>
    </div>
    <div class="environment-details" v-if="currentEnvironment">
      <el-divider content-position="left">环境详情</el-divider>
      <div class="details-content">
        <div class="detail-item">
          <span class="detail-label">通信挑战：</span>
          <span class="detail-value">{{ currentEnvironment.challenges.join('、') }}</span>
        </div>
        <div class="detail-item">
          <span class="detail-label">推荐频段：</span>
          <span class="detail-value">{{ currentEnvironment.recommendedFrequency }}</span>
        </div>
        <div class="detail-item">
          <span class="detail-label">推荐加密：</span>
          <span class="detail-value">{{ currentEnvironment.recommendedEncryption }}</span>
        </div>
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { ref, computed, defineExpose } from 'vue'

const selectedEnvironment = ref('mountain')

const environments = [
  {
    value: 'mountain',
    name: '山地环境',
    icon: '⛰️',
    description: '地形复杂，信号传播受山脉遮挡严重',
    challenges: ['信号遮挡', '地形反射', '远距离'],
    recommendedFrequency: 'HF',
    recommendedEncryption: '跳频'
  },
  {
    value: 'city',
    name: '城市环境',
    icon: '🏙️',
    description: '建筑物密集，电磁环境复杂',
    challenges: ['多径效应', '信号衰减', '电磁干扰'],
    recommendedFrequency: 'UHF',
    recommendedEncryption: '扩频'
  },
  {
    value: 'interference',
    name: '强电磁干扰',
    icon: '⚡',
    description: '敌方干扰严重，通信环境恶劣',
    challenges: ['强电磁干扰', '信号截获', '抗干扰需求高'],
    recommendedFrequency: 'VHF/UHF',
    recommendedEncryption: '跳频'
  }
]

const currentEnvironment = computed(() => {
  return environments.find(env => env.value === selectedEnvironment.value)
})

const setEnvironment = (env) => {
  selectedEnvironment.value = env
}

const getEnvironment = () => {
  return selectedEnvironment.value
}

defineExpose({
  setEnvironment,
  getEnvironment
})
</script>

<style scoped>
.environment-select-card {
  background: #0a0e17;
  border: 1px solid #1f2937;
  border-radius: 16px;
  position: relative;
  overflow: hidden;
  margin-bottom: 20px;
}

.environment-select-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent 0%, #00d4ff 30%, #00ff88 70%, transparent 100%);
  opacity: 0.5;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 0;
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

.header-text {
  flex: 1;
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

.environment-grid {
  margin-top: 24px;
}

.environment-options {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.environment-card {
  background: rgba(31, 41, 55, 0.5);
  border: 2px solid #1f2937;
  border-radius: 12px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  text-align: left;
}

.environment-card:hover {
  background: rgba(31, 41, 55, 0.8);
  border-color: #374151;
  transform: translateY(-2px);
}

.environment-card.is-checked {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-color: #00ff88;
  box-shadow: 0 4px 20px rgba(0, 255, 136, 0.2);
}

.environment-content {
  display: flex;
  gap: 20px;
  align-items: flex-start;
}

.environment-icon {
  font-size: 48px;
  flex-shrink: 0;
}

.environment-info {
  flex: 1;
}

.environment-name {
  font-size: 18px;
  font-weight: 700;
  margin: 0 0 8px 0;
}

.environment-desc {
  font-size: 14px;
  color: #6b7280;
  margin: 0 0 12px 0;
  line-height: 1.5;
}

.environment-challenges {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.challenge-tag {
  padding: 4px 12px;
  background: rgba(0, 212, 255, 0.1);
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 6px;
  font-size: 12px;
  color: #00d4ff;
  font-weight: 500;
}

.environment-details {
  margin-top: 32px;
}

.details-content {
  padding: 20px;
  background: rgba(31, 41, 55, 0.4);
  border-radius: 12px;
  border: 1px solid rgba(31, 41, 55, 0.6);
}

.detail-item {
  display: flex;
  margin-bottom: 12px;
  align-items: flex-start;
}

.detail-item:last-child {
  margin-bottom: 0;
}

.detail-label {
  width: 120px;
  font-size: 14px;
  color: #6b7280;
  font-weight: 600;
  flex-shrink: 0;
}

.detail-value {
  flex: 1;
  font-size: 14px;
  color: #e0e6ed;
  line-height: 1.5;
}
</style>
