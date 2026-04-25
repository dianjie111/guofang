<template>
  <div class="param-config-page">
    <!-- 页面头部 -->
    <div class="page-header">
      <div class="header-badge">
        <span class="badge-icon">⚙️</span>
        <span class="badge-text">参数控制</span>
      </div>
      <h1 class="page-title">通信参数配置</h1>
      <p class="page-subtitle">调整各项参数以适应不同的作战环境</p>
      
      <!-- 环境状态指示器 -->
      <div class="environment-indicator">
        <div class="env-status">
          <span class="env-label">当前环境：</span>
          <span class="env-value">{{ currentEnvironment }}</span>
        </div>
        <div class="signal-strength">
          <span class="signal-label">信号强度：</span>
          <div class="signal-bars">
            <div 
              v-for="i in 5" 
              :key="i"
              class="signal-bar"
              :class="{ active: i <= signalStrength }"
            ></div>
          </div>
        </div>
      </div>
    </div>

    <!-- 配置网格 -->
    <div class="config-grid">
      <!-- 频率参数 -->
      <div class="config-card">
        <div class="card-header">
          <div class="header-icon">📡</div>
          <div class="header-text">
            <h3 class="card-title">频率选择</h3>
            <p class="card-subtitle">选择合适的通信频段</p>
          </div>
          <div class="header-status">
            <div class="status-indicator" :class="frequencyStatus"></div>
          </div>
        </div>
        <div class="card-body">
          <div class="option-grid">
            <div 
              v-for="option in frequencyOptions" 
              :key="option.value"
              class="option-item"
              :class="{ active: form.frequency === option.value }"
              @click="form.frequency = option.value"
            >
              <div class="option-content">
                <div class="option-icon">{{ option.icon }}</div>
                <div class="option-label">{{ option.label }}</div>
                <div class="option-desc">{{ option.shortDesc }}</div>
                <div class="option-badge" :class="option.value">
                  {{ getFrequencyBadge(option.value) }}
                </div>
              </div>
            </div>
          </div>
          <div class="hint-box" v-if="frequencyHint">
            <div class="hint-icon">💡</div>
            <div class="hint-content">
              <div class="hint-title">{{ frequencyHint.title }}</div>
              <div class="hint-text">{{ frequencyHint.text }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 加密方式 -->
      <div class="config-card">
        <div class="card-header">
          <div class="header-icon">🔐</div>
          <div class="header-text">
            <h3 class="card-title">加密方式</h3>
            <p class="card-subtitle">选择安全的通信加密</p>
          </div>
          <div class="header-status">
            <div class="status-indicator" :class="encryptionStatus"></div>
          </div>
        </div>
        <div class="card-body">
          <div class="option-grid">
            <div 
              v-for="option in encryptionOptions" 
              :key="option.value"
              class="option-item"
              :class="{ active: form.encryption === option.value }"
              @click="form.encryption = option.value"
            >
              <div class="option-content">
                <div class="option-icon">{{ option.icon }}</div>
                <div class="option-label">{{ option.label }}</div>
                <div class="option-desc">{{ option.shortDesc }}</div>
                <div class="option-badge" :class="option.value">
                  {{ getEncryptionBadge(option.value) }}
                </div>
              </div>
            </div>
          </div>
          <div class="hint-box" v-if="encryptionHint">
            <div class="hint-icon">💡</div>
            <div class="hint-content">
              <div class="hint-title">{{ encryptionHint.title }}</div>
              <div class="hint-text">{{ encryptionHint.text }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 功率控制 -->
      <div class="config-card full-width">
        <div class="card-header">
          <div class="header-icon">⚡</div>
          <div class="header-text">
            <h3 class="card-title">功率控制</h3>
            <p class="card-subtitle">调整发射功率强度</p>
          </div>
          <div class="header-status">
            <div class="status-indicator" :class="powerStatus"></div>
          </div>
        </div>
        <div class="card-body">
          <div class="power-control">
            <div class="power-display">
              <div class="power-levels">
                <div
                  v-for="i in 10"
                  :key="i"
                  class="power-bar"
                  :class="{ 
                    active: i <= form.power, 
                    warning: i > 8, 
                    low: i <= 2 
                  }"
                >
                  <div class="power-glow" :class="{ active: i <= form.power }"></div>
                </div>
              </div>
              <div class="power-value">
                <div class="power-number">{{ form.power }}</div>
                <div class="power-unit">/10</div>
                <div class="power-label">{{ getPowerLabel(form.power) }}</div>
              </div>
            </div>
            <div class="power-slider-container">
              <input 
                type="range" 
                v-model="form.power" 
                min="1" 
                max="10" 
                step="1"
                class="power-slider"
              />
              <div class="slider-labels">
                <span class="label-left">低功率</span>
                <span class="label-center">5</span>
                <span class="label-right">高功率</span>
              </div>
            </div>
          </div>
          <div class="hint-box" v-if="powerHint">
            <div class="hint-icon">💡</div>
            <div class="hint-content">
              <div class="hint-title">{{ powerHint.title }}</div>
              <div class="hint-text">{{ powerHint.text }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 抗干扰模式 -->
      <div class="config-card">
        <div class="card-header">
          <div class="header-icon">🛡️</div>
          <div class="header-text">
            <h3 class="card-title">抗干扰模式</h3>
            <p class="card-subtitle">增强通信抗干扰能力</p>
          </div>
          <div class="header-status">
            <div class="status-indicator" :class="antiInterferenceStatus"></div>
          </div>
        </div>
        <div class="card-body">
          <div class="option-grid">
            <div 
              v-for="option in antiInterferenceOptions" 
              :key="option.value"
              class="option-item"
              :class="{ active: form.antiInterference === option.value }"
              @click="form.antiInterference = option.value"
            >
              <div class="option-content">
                <div class="option-icon">{{ option.icon }}</div>
                <div class="option-label">{{ option.label }}</div>
                <div class="option-desc">{{ option.shortDesc }}</div>
                <div class="option-badge" :class="option.value">
                  {{ getAntiInterferenceBadge(option.value) }}
                </div>
              </div>
            </div>
          </div>
          <div class="hint-box" v-if="antiInterferenceHint">
            <div class="hint-icon">💡</div>
            <div class="hint-content">
              <div class="hint-title">{{ antiInterferenceHint.title }}</div>
              <div class="hint-text">{{ antiInterferenceHint.text }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 配置效果预览 -->
      <div class="config-card full-width">
        <div class="card-header">
          <div class="header-icon">📊</div>
          <div class="header-text">
            <h3 class="card-title">配置效果预览</h3>
            <p class="card-subtitle">实时评估当前配置的通信质量</p>
          </div>
        </div>
        <div class="card-body">
          <div class="preview-grid">
            <div class="preview-item">
              <div class="preview-label">通信距离</div>
              <div class="preview-value">{{ communicationRange }} km</div>
              <div class="preview-bar">
                <div class="preview-fill" :style="{ width: communicationRange + '%' }"></div>
              </div>
            </div>
            <div class="preview-item">
              <div class="preview-label">抗干扰能力</div>
              <div class="preview-value">{{ antiInterferenceLevel }}%</div>
              <div class="preview-bar">
                <div class="preview-fill" :style="{ width: antiInterferenceLevel + '%' }"></div>
              </div>
            </div>
            <div class="preview-item">
              <div class="preview-label">隐蔽性</div>
              <div class="preview-value">{{ stealthLevel }}%</div>
              <div class="preview-bar">
                <div class="preview-fill" :style="{ width: stealthLevel + '%' }"></div>
              </div>
            </div>
            <div class="preview-item">
              <div class="preview-label">能耗</div>
              <div class="preview-value">{{ powerConsumption }}%</div>
              <div class="preview-bar">
                <div class="preview-fill" :style="{ width: powerConsumption + '%' }"></div>
              </div>
            </div>
          </div>
          <div class="overall-assessment">
            <div class="assessment-label">整体评估：</div>
            <div class="assessment-score" :class="getAssessmentClass(overallScore)">
              {{ overallScore }}/100
            </div>
            <div class="assessment-text">{{ getAssessmentText(overallScore) }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// 表单数据
const form = ref({
  frequency: 'vhf',
  encryption: 'frequency-hopping',
  power: 5,
  antiInterference: 'adaptive-fm'
})

// 环境和信号强度
const currentEnvironment = ref('城市环境')
const signalStrength = ref(4)

// 频率选项
const frequencyOptions = [
  {
    value: 'hf',
    label: 'HF (高频)',
    icon: '🏔️',
    shortDesc: '远距离'
  },
  {
    value: 'vhf',
    label: 'VHF (甚高频)',
    icon: '📻',
    shortDesc: '分队通信'
  },
  {
    value: 'uhf',
    label: 'UHF (特高频)',
    icon: '🏙️',
    shortDesc: '城市环境'
  }
]

// 加密选项
const encryptionOptions = [
  {
    value: 'frequency-hopping',
    label: '跳频',
    icon: '🔄',
    shortDesc: '抗干扰'
  },
  {
    value: 'spread-spectrum',
    label: '扩频',
    icon: '🔇',
    shortDesc: '低截获'
  },
  {
    value: 'normal',
    label: '常规加密',
    icon: '🔒',
    shortDesc: '基础'
  }
]

// 抗干扰选项
const antiInterferenceOptions = [
  {
    value: 'adaptive-fm',
    label: '自适应调频',
    icon: '🎯',
    shortDesc: '自动调整'
  },
  {
    value: 'error-correction',
    label: '纠错编码',
    icon: '✅',
    shortDesc: '高可靠'
  },
  {
    value: 'none',
    label: '无',
    icon: '❌',
    shortDesc: '基础'
  }
]

// 频率提示
const frequencyHint = computed(() => {
  switch (form.value.frequency) {
    case 'hf':
      return {
        title: 'HF频段',
        text: '通过电离层反射实现超视距传输，适合山地和远距离通信，但受太阳活动影响较大'
      }
    case 'vhf':
      return {
        title: 'VHF频段',
        text: '视距传播，抗干扰能力较好，适合近距离分队通信，但易受地形遮挡'
      }
    case 'uhf':
      return {
        title: 'UHF频段',
        text: '穿透能力强，带宽大、数据传输快，适合城市环境和复杂地形'
      }
    default:
      return null
  }
})

// 加密提示
const encryptionHint = computed(() => {
  switch (form.value.encryption) {
    case 'frequency-hopping':
      return {
        title: '跳频技术',
        text: '通信双方按照伪随机序列同步改变载波频率，每秒跳变可达20000跳以上，抗干扰能力强'
      }
    case 'spread-spectrum':
      return {
        title: '扩频技术',
        text: '将信号扩展到更宽的频带，使信号"淹没"在噪声中，低截获概率，适合隐蔽通信'
      }
    case 'normal':
      return {
        title: '常规加密',
        text: '基础加密方式，抗干扰能力较弱，适合低威胁环境'
      }
    default:
      return null
  }
})

// 功率提示
const powerHint = computed(() => {
  if (form.value.power < 3) {
    return {
      title: '低功率模式',
      text: '通信距离受限，但能耗低、隐蔽性好，适合近距离隐蔽通信'
    }
  } else if (form.value.power > 8) {
    return {
      title: '高功率模式',
      text: '通信距离远，但能耗较大、易被敌方探测，适合紧急远距离通信'
    }
  } else {
    return {
      title: '适中功率',
      text: '平衡通信距离和能耗，适合大多数常规作战场景'
    }
  }
})

// 抗干扰提示
const antiInterferenceHint = computed(() => {
  switch (form.value.antiInterference) {
    case 'adaptive-fm':
      return {
        title: '自适应调频',
        text: '实时监测干扰并自动调整工作频率，抗干扰能力强，适合复杂电磁环境'
      }
    case 'error-correction':
      return {
        title: '纠错编码',
        text: '通过冗余编码检测和纠正传输错误，提高数据传输可靠性'
      }
    case 'none':
      return {
        title: '无抗干扰保护',
        text: '通信系统易受干扰，仅适合电磁环境干净的区域'
      }
    default:
      return null
  }
})

// 状态指示器
const frequencyStatus = computed(() => {
  switch (form.value.frequency) {
    case 'hf': return 'excellent'
    case 'vhf': return 'good'
    case 'uhf': return 'excellent'
    default: return 'good'
  }
})

const encryptionStatus = computed(() => {
  switch (form.value.encryption) {
    case 'frequency-hopping': return 'excellent'
    case 'spread-spectrum': return 'excellent'
    case 'normal': return 'warning'
    default: return 'good'
  }
})

const powerStatus = computed(() => {
  if (form.value.power < 3) return 'info'
  if (form.value.power > 8) return 'warning'
  return 'good'
})

const antiInterferenceStatus = computed(() => {
  switch (form.value.antiInterference) {
    case 'adaptive-fm': return 'excellent'
    case 'error-correction': return 'good'
    case 'none': return 'danger'
    default: return 'good'
  }
})

// 徽章文本
const getFrequencyBadge = (value) => {
  const badges = {
    hf: '远距离',
    vhf: '中距离',
    uhf: '短距离'
  }
  return badges[value] || ''
}

const getEncryptionBadge = (value) => {
  const badges = {
    'frequency-hopping': '高抗干扰',
    'spread-spectrum': '低截获',
    'normal': '基础安全'
  }
  return badges[value] || ''
}

const getAntiInterferenceBadge = (value) => {
  const badges = {
    'adaptive-fm': '智能抗干扰',
    'error-correction': '高可靠性',
    'none': '无保护'
  }
  return badges[value] || ''
}

// 功率标签
const getPowerLabel = (power) => {
  if (power < 3) return '低功率'
  if (power > 8) return '高功率'
  return '适中功率'
}

// 配置效果预览
const communicationRange = computed(() => {
  let base = 50
  if (form.value.frequency === 'hf') base += 30
  if (form.value.power > 7) base += 20
  if (form.value.power < 3) base -= 20
  return Math.min(100, Math.max(10, base))
})

const antiInterferenceLevel = computed(() => {
  let base = 50
  if (form.value.encryption === 'frequency-hopping') base += 30
  if (form.value.encryption === 'spread-spectrum') base += 25
  if (form.value.antiInterference === 'adaptive-fm') base += 20
  if (form.value.antiInterference === 'error-correction') base += 15
  if (form.value.antiInterference === 'none') base -= 20
  return Math.min(100, Math.max(10, base))
})

const stealthLevel = computed(() => {
  let base = 50
  if (form.value.encryption === 'spread-spectrum') base += 30
  if (form.value.power < 3) base += 20
  if (form.value.power > 8) base -= 20
  return Math.min(100, Math.max(10, base))
})

const powerConsumption = computed(() => {
  let base = 50
  if (form.value.power > 8) base += 30
  if (form.value.power < 3) base -= 20
  return Math.min(100, Math.max(10, base))
})

// 整体评分
const overallScore = computed(() => {
  const scores = [
    communicationRange.value * 0.3,
    antiInterferenceLevel.value * 0.35,
    stealthLevel.value * 0.2,
    (100 - powerConsumption.value) * 0.15
  ]
  return Math.round(scores.reduce((a, b) => a + b, 0))
})

// 评估等级
const getAssessmentClass = (score) => {
  if (score >= 90) return 'excellent'
  if (score >= 75) return 'good'
  if (score >= 60) return 'average'
  return 'poor'
}

const getAssessmentText = (score) => {
  if (score >= 90) return '优秀配置，适合复杂作战环境'
  if (score >= 75) return '良好配置，适合常规作战环境'
  if (score >= 60) return '基本配置，适合低威胁环境'
  return '配置需要优化'
}

// 暴露方法
defineExpose({
  getParams: () => form.value,
  form
})
</script>

<style scoped>
.param-config-page {
  padding: 0;
  position: relative;
  min-height: 100vh;
}

/* 背景效果 */
.param-config-page::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: 
    radial-gradient(circle at 10% 20%, rgba(0, 255, 136, 0.03) 0%, transparent 20%),
    radial-gradient(circle at 90% 80%, rgba(0, 212, 255, 0.03) 0%, transparent 20%),
    linear-gradient(135deg, #0a0e17 0%, #0d1118 100%);
  pointer-events: none;
  z-index: -1;
}

/* 页面头部 */
.page-header {
  margin-bottom: 40px;
  text-align: left;
  position: relative;
  padding-bottom: 24px;
  border-bottom: 1px solid rgba(31, 41, 55, 0.5);
}

.page-header::after {
  content: '';
  position: absolute;
  bottom: -1px;
  left: 0;
  width: 100%;
  height: 1px;
  background: linear-gradient(90deg, transparent 0%, #00ff88 20%, #00d4ff 50%, #00ff88 80%, transparent 100%);
  opacity: 0.5;
}

.header-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba(0, 212, 255, 0.1);
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 50px;
  margin-bottom: 16px;
  backdrop-filter: blur(10px);
}

.badge-icon {
  color: #00d4ff;
  font-size: 16px;
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}

.badge-text {
  font-size: 12px;
  color: #00d4ff;
  letter-spacing: 2px;
  font-weight: 600;
  text-transform: uppercase;
}

.page-title {
  font-size: 36px;
  font-weight: 800;
  margin: 0 0 8px 0;
  letter-spacing: 1px;
  background: linear-gradient(90deg, #ffffff 0%, #a1a1aa 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.page-subtitle {
  font-size: 15px;
  color: #6b7280;
  margin: 0 0 24px 0;
  letter-spacing: 0.5px;
}

/* 环境指示器 */
.environment-indicator {
  display: flex;
  align-items: center;
  gap: 40px;
  padding: 16px 24px;
  background: rgba(31, 41, 55, 0.3);
  border: 1px solid rgba(31, 41, 55, 0.5);
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

.env-status {
  display: flex;
  align-items: center;
  gap: 12px;
}

.env-label {
  font-size: 14px;
  color: #9ca3af;
  font-weight: 500;
}

.env-value {
  font-size: 14px;
  font-weight: 600;
  color: #00ff88;
}

.signal-strength {
  display: flex;
  align-items: center;
  gap: 12px;
}

.signal-label {
  font-size: 14px;
  color: #9ca3af;
  font-weight: 500;
}

.signal-bars {
  display: flex;
  gap: 4px;
  align-items: flex-end;
}

.signal-bar {
  width: 8px;
  background: rgba(31, 41, 55, 0.5);
  border-radius: 4px;
  transition: all 0.3s ease;
}

.signal-bar:nth-child(1) { height: 12px; }
.signal-bar:nth-child(2) { height: 16px; }
.signal-bar:nth-child(3) { height: 20px; }
.signal-bar:nth-child(4) { height: 24px; }
.signal-bar:nth-child(5) { height: 28px; }

.signal-bar.active {
  background: linear-gradient(180deg, #00ff88 0%, #00cc6a 100%);
  box-shadow: 0 0 8px rgba(0, 255, 136, 0.5);
}

/* 配置网格 */
.config-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;
  max-width: 1400px;
  margin: 0 auto;
}

.config-card {
  background: rgba(10, 14, 23, 0.8);
  border: 1px solid rgba(31, 41, 55, 0.5);
  border-radius: 20px;
  position: relative;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  backdrop-filter: blur(10px);
}

.config-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent 0%, #00d4ff 30%, #00ff88 70%, transparent 100%);
  opacity: 0.6;
  animation: gradient-flow 4s ease-in-out infinite;
}

@keyframes gradient-flow {
  0%, 100% { transform: translateX(-100%); }
  50% { transform: translateX(100%); }
}

.config-card:hover {
  border-color: rgba(0, 255, 136, 0.3);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.4);
  transform: translateY(-4px);
}

.config-card.full-width {
  grid-column: 1 / -1;
}

/* 卡片头部 */
.card-header {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 24px 24px 16px;
  border-bottom: 1px solid rgba(31, 41, 55, 0.3);
}

.header-icon {
  width: 56px;
  height: 56px;
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.15) 0%, rgba(0, 255, 136, 0.15) 100%);
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  flex-shrink: 0;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
}

.header-text {
  flex: 1;
  min-width: 0;
}

.card-title {
  font-size: 20px;
  font-weight: 700;
  margin: 0 0 4px 0;
  letter-spacing: 0.5px;
}

.card-subtitle {
  font-size: 13px;
  color: #6b7280;
  margin: 0;
  letter-spacing: 0.5px;
}

.header-status {
  flex-shrink: 0;
}

.status-indicator {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  transition: all 0.3s ease;
}

.status-indicator.excellent {
  background: #00ff88;
  box-shadow: 0 0 10px rgba(0, 255, 136, 0.6);
}

.status-indicator.good {
  background: #00d4ff;
  box-shadow: 0 0 10px rgba(0, 212, 255, 0.6);
}

.status-indicator.warning {
  background: #ffa500;
  box-shadow: 0 0 10px rgba(255, 165, 0, 0.6);
}

.status-indicator.danger {
  background: #ef4444;
  box-shadow: 0 0 10px rgba(239, 68, 68, 0.6);
}

.status-indicator.info {
  background: #60a5fa;
  box-shadow: 0 0 10px rgba(96, 165, 250, 0.6);
}

/* 卡片内容 */
.card-body {
  padding: 24px;
  position: relative;
  z-index: 1;
}

/* 选项网格 */
.option-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  margin-bottom: 20px;
}

.option-item {
  background: rgba(31, 41, 55, 0.3);
  border: 2px solid rgba(31, 41, 55, 0.5);
  border-radius: 16px;
  padding: 20px 16px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  text-align: center;
  position: relative;
  overflow: hidden;
}

.option-item::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent 0%, rgba(0, 255, 136, 0.1) 50%, transparent 100%);
  transition: left 0.6s ease;
}

.option-item:hover::before {
  left: 100%;
}

.option-item:hover {
  background: rgba(31, 41, 55, 0.5);
  border-color: rgba(0, 255, 136, 0.3);
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
}

.option-item.active {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.1) 0%, rgba(0, 212, 255, 0.1) 100%);
  border-color: #00ff88;
  box-shadow: 0 8px 32px rgba(0, 255, 136, 0.2);
  transform: translateY(-2px);
}

.option-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  position: relative;
  z-index: 1;
}

.option-icon {
  font-size: 32px;
  transition: transform 0.3s ease;
}

.option-item:hover .option-icon {
  transform: scale(1.1);
}

.option-label {
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.5px;
}

.option-desc {
  font-size: 12px;
  color: #6b7280;
  letter-spacing: 0.5px;
}

.option-badge {
  padding: 4px 12px;
  border-radius: 50px;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  margin-top: 8px;
}

.option-badge.hf {
  background: rgba(102, 194, 58, 0.2);
  color: #00ff88;
  border: 1px solid rgba(102, 194, 58, 0.3);
}

.option-badge.vhf {
  background: rgba(96, 165, 250, 0.2);
  color: #60a5fa;
  border: 1px solid rgba(96, 165, 250, 0.3);
}

.option-badge.uhf {
  background: rgba(236, 72, 153, 0.2);
  color: #ec4899;
  border: 1px solid rgba(236, 72, 153, 0.3);
}

.option-badge.frequency-hopping {
  background: rgba(245, 158, 11, 0.2);
  color: #f59e0b;
  border: 1px solid rgba(245, 158, 11, 0.3);
}

.option-badge.spread-spectrum {
  background: rgba(168, 85, 247, 0.2);
  color: #a855f7;
  border: 1px solid rgba(168, 85, 247, 0.3);
}

.option-badge.normal {
  background: rgba(156, 163, 175, 0.2);
  color: #9ca3af;
  border: 1px solid rgba(156, 163, 175, 0.3);
}

.option-badge.adaptive-fm {
  background: rgba(16, 185, 129, 0.2);
  color: #10b981;
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.option-badge.error-correction {
  background: rgba(59, 130, 246, 0.2);
  color: #3b82f6;
  border: 1px solid rgba(59, 130, 246, 0.3);
}

.option-badge.none {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

/* 功率控制 */
.power-control {
  margin-bottom: 20px;
}

.power-display {
  display: flex;
  align-items: center;
  gap: 32px;
  margin-bottom: 24px;
}

.power-levels {
  display: flex;
  gap: 6px;
  flex: 1;
}

.power-bar {
  flex: 1;
  height: 60px;
  background: rgba(31, 41, 55, 0.5);
  border-radius: 8px;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
}

.power-bar.active {
  background: linear-gradient(180deg, #00ff88 0%, #00cc6a 100%);
  box-shadow: 0 0 20px rgba(0, 255, 136, 0.3);
}

.power-bar.active.warning {
  background: linear-gradient(180deg, #ffa500 0%, #cc8400 100%);
  box-shadow: 0 0 20px rgba(255, 165, 0, 0.3);
}

.power-bar.active.low {
  background: linear-gradient(180deg, #00d4ff 0%, #0099cc 100%);
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.3);
}

.power-glow {
  position: absolute;
  bottom: -20px;
  left: 0;
  width: 100%;
  height: 20px;
  background: linear-gradient(180deg, rgba(0, 255, 136, 0.8) 0%, transparent 100%);
  opacity: 0;
  transition: opacity 0.3s ease;
  filter: blur(10px);
}

.power-glow.active {
  opacity: 1;
}

.power-value {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  flex-shrink: 0;
  min-width: 100px;
}

.power-number {
  font-size: 48px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  color: #00ff88;
  text-shadow: 0 0 20px rgba(0, 255, 136, 0.5);
  line-height: 1;
}

.power-unit {
  font-size: 16px;
  color: #6b7280;
  font-family: 'Courier New', monospace;
}

.power-label {
  font-size: 12px;
  color: #9ca3af;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.power-slider-container {
  position: relative;
}

.power-slider {
  width: 100%;
  height: 8px;
  border-radius: 4px;
  background: rgba(31, 41, 55, 0.5);
  outline: none;
  -webkit-appearance: none;
  margin: 24px 0;
}

.power-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  cursor: pointer;
  box-shadow: 0 0 16px rgba(0, 255, 136, 0.4);
  transition: all 0.3s ease;
}

.power-slider::-webkit-slider-thumb:hover {
  transform: scale(1.2);
  box-shadow: 0 0 24px rgba(0, 255, 136, 0.6);
}

.power-slider::-moz-range-thumb {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  cursor: pointer;
  border: none;
  box-shadow: 0 0 16px rgba(0, 255, 136, 0.4);
  transition: all 0.3s ease;
}

.power-slider::-moz-range-thumb:hover {
  transform: scale(1.2);
  box-shadow: 0 0 24px rgba(0, 255, 136, 0.6);
}

.slider-labels {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #6b7280;
  margin-top: -16px;
}

.label-center {
  font-weight: 600;
  color: #9ca3af;
}

/* 提示框 */
.hint-box {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px 20px;
  background: rgba(0, 212, 255, 0.08);
  border: 1px solid rgba(0, 212, 255, 0.15);
  border-radius: 12px;
  backdrop-filter: blur(10px);
  transition: all 0.3s ease;
}

.hint-box:hover {
  background: rgba(0, 212, 255, 0.12);
  border-color: rgba(0, 212, 255, 0.25);
}

.hint-icon {
  font-size: 20px;
  flex-shrink: 0;
  margin-top: 2px;
}

.hint-content {
  flex: 1;
  min-width: 0;
}

.hint-title {
  font-size: 14px;
  font-weight: 600;
  margin: 0 0 4px 0;
  color: #00d4ff;
}

.hint-text {
  font-size: 13px;
  color: #9ca3af;
  line-height: 1.5;
  margin: 0;
}

/* 配置效果预览 */
.preview-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  margin-bottom: 32px;
}

.preview-item {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.preview-label {
  font-size: 13px;
  color: #6b7280;
  font-weight: 500;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.preview-value {
  font-size: 24px;
  font-weight: 700;
  font-family: 'Courier New', monospace;
  color: #00ff88;
  line-height: 1;
}

.preview-bar {
  height: 6px;
  background: rgba(31, 41, 55, 0.5);
  border-radius: 3px;
  overflow: hidden;
  position: relative;
}

.preview-fill {
  height: 100%;
  background: linear-gradient(90deg, #00d4ff 0%, #00ff88 100%);
  border-radius: 3px;
  transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
}

.preview-fill::after {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent 0%, rgba(255, 255, 255, 0.4) 50%, transparent 100%);
  animation: shimmer 2s ease-in-out infinite;
}

@keyframes shimmer {
  0% { left: -100%; }
  100% { left: 100%; }
}

.overall-assessment {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px;
  background: rgba(31, 41, 55, 0.3);
  border: 1px solid rgba(31, 41, 55, 0.5);
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

.assessment-label {
  font-size: 14px;
  color: #9ca3af;
  font-weight: 500;
}

.assessment-score {
  font-size: 28px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  padding: 8px 20px;
  border-radius: 50px;
  transition: all 0.3s ease;
}

.assessment-score.excellent {
  background: rgba(0, 255, 136, 0.2);
  color: #00ff88;
  border: 1px solid rgba(0, 255, 136, 0.3);
}

.assessment-score.good {
  background: rgba(0, 212, 255, 0.2);
  color: #00d4ff;
  border: 1px solid rgba(0, 212, 255, 0.3);
}

.assessment-score.average {
  background: rgba(255, 165, 0, 0.2);
  color: #ffa500;
  border: 1px solid rgba(255, 165, 0, 0.3);
}

.assessment-score.poor {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.assessment-text {
  font-size: 14px;
  color: #9ca3af;
  font-weight: 500;
  flex: 1;
}

/* 响应式设计 */
@media (max-width: 1024px) {
  .config-grid {
    grid-template-columns: 1fr;
  }
  
  .preview-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .environment-indicator {
    flex-direction: column;
    align-items: flex-start;
    gap: 12px;
  }
}

@media (max-width: 768px) {
  .page-header {
    padding-bottom: 20px;
  }
  
  .page-title {
    font-size: 28px;
  }
  
  .option-grid {
    grid-template-columns: 1fr;
  }
  
  .power-display {
    flex-direction: column;
    align-items: stretch;
    gap: 20px;
  }
  
  .power-value {
    flex-direction: row;
    justify-content: center;
    gap: 12px;
  }
  
  .preview-grid {
    grid-template-columns: 1fr;
  }
  
  .overall-assessment {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
    text-align: center;
  }
}
</style>
