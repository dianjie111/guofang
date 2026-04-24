<template>
  <div class="quality-assessment-card">
    <div class="card-header">
      <div class="header-icon">📊</div>
      <div class="header-text">
        <h3 class="card-title">通信质量评估</h3>
        <p class="card-subtitle">评估当前参数配置的通信质量</p>
      </div>
      <div class="header-actions">
        <button class="action-btn secondary" @click="calculateQuality">
          <span class="btn-icon">📈</span>
          <span>计算评估</span>
        </button>
        <button class="action-btn primary" @click="applyRecommendedConfig">
          <span class="btn-icon">✨</span>
          <span>智能优化</span>
        </button>
      </div>
    </div>
    <div class="assessment-result" v-if="showResult">
      <div class="result-section">
        <div class="result-header">
          <span class="section-icon">🎯</span>
          <span class="section-title">评估结果</span>
        </div>
        <div class="score-container">
          <div class="score-circle">
            <svg class="progress-ring" width="180" height="180">
              <circle
                class="progress-ring-circle-bg"
                cx="90"
                cy="90"
                r="75"
              ></circle>
              <circle
                class="progress-ring-circle"
                cx="90"
                cy="90"
                r="75"
                :style="{ 
                  strokeDasharray: `${circumference} ${circumference}`,
                  strokeDashoffset: `${offset}`,
                  stroke: `${scoreColor}`
                }"
              ></circle>
            </svg>
            <div class="score-inner">
              <span class="score-value">{{ score }}</span>
              <span class="score-unit">/100</span>
            </div>
          </div>
          <div class="score-info">
            <h3>通信质量评分</h3>
            <div class="score-level-badge" :class="`level-${scoreLevel.toLowerCase()}`">
              <span class="level-icon">{{ levelIcon }}</span>
              <span>{{ scoreLevel }}</span>
            </div>
          </div>
        </div>
      </div>
      
      <div class="suggestions-section">
        <div class="result-header">
          <span class="section-icon">💡</span>
          <span class="section-title">优化建议</span>
        </div>
        <div class="suggestions-list">
          <div 
            v-for="(suggestion, index) in suggestions" 
            :key="index"
            class="suggestion-item"
          >
            <span class="suggestion-icon">📌</span>
            <span class="suggestion-text">{{ suggestion }}</span>
          </div>
        </div>
      </div>
      
      <div class="comparison-section" v-if="showComparison">
        <div class="result-header">
          <span class="section-icon">⚖️</span>
          <span class="section-title">配置对比</span>
        </div>
        <div class="comparison-grid">
          <div 
            v-for="item in comparisonData" 
            :key="item.param"
            class="comparison-item"
          >
            <div class="comparison-label">{{ item.param }}</div>
            <div class="comparison-values">
              <div class="value-column">
                <span class="value-label">当前</span>
                <span class="value-current">{{ item.current }}</span>
              </div>
              <div class="value-divider">→</div>
              <div class="value-column">
                <span class="value-label">推荐</span>
                <span class="value-recommended">{{ item.recommended }}</span>
              </div>
            </div>
            <div class="comparison-status" :class="item.status.type">
              <span class="status-icon">{{ item.status.icon }}</span>
              <span class="status-text">{{ item.status.text }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, defineProps } from 'vue'
import { ElMessage } from 'element-plus'

const props = defineProps({
  paramConfigRef: {
    type: Object,
    default: null
  },
  environmentSelectRef: {
    type: Object,
    default: null
  }
})

const showResult = ref(false)
const showComparison = ref(true)
const score = ref(0)
const suggestions = ref([])
const comparisonData = ref([])

const circumference = 2 * Math.PI * 75
const offset = computed(() => circumference - (score.value / 100) * circumference)

const scoreColor = computed(() => {
  if (score.value >= 80) return '#00ff88'
  if (score.value >= 60) return '#ffa500'
  return '#ff6b6b'
})

const scoreLevel = computed(() => {
  if (score.value >= 80) return '优秀'
  if (score.value >= 60) return '良好'
  return '较差'
})

const levelIcon = computed(() => {
  if (score.value >= 80) return '🏆'
  if (score.value >= 60) return '👍'
  return '⚠️'
})

const calculateQuality = () => {
  if (!props.paramConfigRef || !props.environmentSelectRef) {
    score.value = 0
    suggestions.value = ['无法获取参数配置或环境信息']
    showResult.value = true
    return
  }

  const params = props.paramConfigRef.getParams()
  const environment = props.environmentSelectRef.getEnvironment()
  
  // 基础分数
  let baseScore = 30
  
  if (window.currentTask) {
    const recommendedConfig = window.currentTask.recommendedConfig.reduce((acc, item) => {
      acc[item.param] = item.value
      return acc
    }, {})
    
    // 频段评分
    if (params.frequency === 'hf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('HF')) {
      baseScore += 30
    } else if (params.frequency === 'uhf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('UHF')) {
      baseScore += 30
    } else if (params.frequency === 'vhf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('VHF')) {
      baseScore += 30
    } else {
      baseScore += 5
    }
    
    // 加密方式评分
    if (params.encryption === 'frequency-hopping' && recommendedConfig['加密方式'] && recommendedConfig['加密方式'].includes('跳频')) {
      baseScore += 25
    } else if (params.encryption === 'spread-spectrum' && recommendedConfig['加密方式'] && recommendedConfig['加密方式'].includes('扩频')) {
      baseScore += 25
    } else {
      baseScore += 5
    }
    
    // 功率评分
    const recommendedPower = recommendedConfig['功率']
    if (recommendedPower) {
      const powerRange = recommendedPower.split('-').map(p => parseInt(p))
      if (powerRange.length === 2) {
        const [min, max] = powerRange
        if (params.power >= min && params.power <= max) {
          baseScore += 20
        } else if (params.power >= min - 1 && params.power <= max + 1) {
          baseScore += 10
        } else {
          baseScore += 5
        }
      }
    } else {
      baseScore += params.power * 1.5
    }
    
    // 抗干扰模式评分
    if (params.antiInterference === 'adaptive-fm' && recommendedConfig['抗干扰模式'] && recommendedConfig['抗干扰模式'].includes('自适应')) {
      baseScore += 20
    } else if (params.antiInterference === 'error-correction' && recommendedConfig['抗干扰模式'] && recommendedConfig['抗干扰模式'].includes('纠错')) {
      baseScore += 20
    } else {
      baseScore += 5
    }
  } else {
    // 非任务模式下的评分
    if (environment === 'mountain' && params.frequency === 'hf') {
      baseScore += 20
    } else if (environment === 'city' && params.frequency === 'uhf') {
      baseScore += 20
    } else if (environment === 'interference' && (params.frequency === 'vhf' || params.frequency === 'uhf')) {
      baseScore += 15
    } else {
      baseScore += 5
    }
    
    if (params.encryption === 'frequency-hopping') {
      baseScore += 15
    } else if (params.encryption === 'spread-spectrum') {
      baseScore += 10
    } else {
      baseScore += 5
    }
    
    baseScore += params.power * 1.5
    
    if (params.antiInterference === 'adaptive-fm') {
      baseScore += 10
    } else if (params.antiInterference === 'error-correction') {
      baseScore += 8
    }
  }
  
  if (environment === 'interference' && params.encryption === 'frequency-hopping') {
    baseScore += 10
  } else if (environment === 'city' && params.encryption === 'spread-spectrum') {
    baseScore += 8
  }
  
  score.value = Math.min(100, Math.max(0, baseScore))
  
  suggestions.value = []
  if (environment === 'mountain' && params.frequency !== 'hf') {
    suggestions.value.push('山地环境建议使用HF频段，以利用电离层反射实现超视距传输')
  }
  if (environment === 'city' && params.frequency !== 'uhf') {
    suggestions.value.push('城市环境建议使用UHF频段，以利用其较强的穿透能力')
  }
  if (environment === 'interference' && params.encryption !== 'frequency-hopping') {
    suggestions.value.push('强电磁干扰环境建议使用跳频加密，以提高抗干扰能力')
  }
  if (params.power < 3) {
    suggestions.value.push('功率较低，可能影响通信距离，建议适当提高功率')
  } else if (params.power > 8) {
    suggestions.value.push('功率较高，虽然通信质量好，但能耗较大，建议根据实际需求调整')
  }
  if (params.antiInterference === 'none') {
    suggestions.value.push('未开启抗干扰模式，建议根据环境开启相应的抗干扰技术')
  }
  
  if (suggestions.value.length === 0) {
    suggestions.value.push('当前参数配置合理，适合所选环境')
  }
  
  generateComparisonData(params, environment)
  
  if (window.historyRecordRef) {
    window.historyRecordRef.addRecord({
      environment,
      frequency: params.frequency,
      encryption: params.encryption,
      power: params.power,
      score: score.value,
      taskId: window.currentTaskId
    })
  }
  
  showResult.value = true
  
  if (window.currentTask && score.value >= 70) {
    ElMessage({
      message: '任务通过！正在返回首页...',
      type: 'success',
      duration: 3000
    })
    
    const taskEnvironment = window.currentTask.environment || 'mountain'
    const taskName = window.currentTask.title || '未知任务'
    
    setTimeout(() => {
      if (window.completeCurrentTask) {
        window.completeCurrentTask()
      }
      if (window.updateBestScore) {
        window.updateBestScore(taskEnvironment, score.value)
      }
      if (window.addTrainingScore) {
        window.addTrainingScore(taskName, score.value)
      }
      if (window.switchToHome) {
        window.switchToHome()
      }
    }, 3000)
  } else if (window.currentTask && score.value < 70) {
    ElMessage({
      message: '任务未通过，请调整参数后重新尝试',
      type: 'warning',
      duration: 3000
    })
  }
}

const generateComparisonData = (params, environment) => {
  const recommended = getRecommendedConfig(environment)
  comparisonData.value = [
    {
      param: '频率',
      current: getFrequencyName(params.frequency),
      recommended: getFrequencyName(recommended.frequency),
      status: params.frequency === recommended.frequency 
        ? { type: 'success', icon: '✅', text: '匹配' } 
        : { type: 'warning', icon: '🔄', text: '建议切换' }
    },
    {
      param: '加密方式',
      current: getEncryptionName(params.encryption),
      recommended: getEncryptionName(recommended.encryption),
      status: params.encryption === recommended.encryption 
        ? { type: 'success', icon: '✅', text: '匹配' } 
        : { type: 'warning', icon: '🔄', text: '建议切换' }
    },
    {
      param: '功率',
      current: params.power,
      recommended: recommended.power,
      status: params.power === recommended.power 
        ? { type: 'success', icon: '✅', text: '匹配' } 
        : { type: 'warning', icon: '🔄', text: '建议调整' }
    },
    {
      param: '抗干扰模式',
      current: getAntiInterferenceName(params.antiInterference),
      recommended: getAntiInterferenceName(recommended.antiInterference),
      status: params.antiInterference === recommended.antiInterference 
        ? { type: 'success', icon: '✅', text: '匹配' } 
        : { type: 'warning', icon: '🔄', text: '建议切换' }
    }
  ]
}

const getRecommendedConfig = (environment) => {
  switch (environment) {
    case 'mountain':
      return {
        frequency: 'hf',
        encryption: 'frequency-hopping',
        power: 7,
        antiInterference: 'adaptive-fm'
      }
    case 'city':
      return {
        frequency: 'uhf',
        encryption: 'spread-spectrum',
        power: 5,
        antiInterference: 'error-correction'
      }
    case 'interference':
      return {
        frequency: 'vhf',
        encryption: 'frequency-hopping',
        power: 8,
        antiInterference: 'adaptive-fm'
      }
    default:
      return {
        frequency: 'vhf',
        encryption: 'frequency-hopping',
        power: 5,
        antiInterference: 'adaptive-fm'
      }
  }
}

const getFrequencyName = (frequency) => {
  switch (frequency) {
    case 'hf': return 'HF (高频)'
    case 'vhf': return 'VHF (甚高频)'
    case 'uhf': return 'UHF (特高频)'
    default: return frequency
  }
}

const getEncryptionName = (encryption) => {
  switch (encryption) {
    case 'frequency-hopping': return '跳频'
    case 'spread-spectrum': return '扩频'
    case 'normal': return '常规加密'
    default: return encryption
  }
}

const getAntiInterferenceName = (antiInterference) => {
  switch (antiInterference) {
    case 'adaptive-fm': return '自适应调频'
    case 'error-correction': return '纠错编码'
    case 'none': return '无'
    default: return antiInterference
  }
}

const applyRecommendedConfig = () => {
  if (!props.paramConfigRef || !props.environmentSelectRef) return
  
  const environment = props.environmentSelectRef.getEnvironment()
  const recommended = getRecommendedConfig(environment)
  
  props.paramConfigRef.form.frequency = recommended.frequency
  props.paramConfigRef.form.encryption = recommended.encryption
  props.paramConfigRef.form.power = recommended.power
  props.paramConfigRef.form.antiInterference = recommended.antiInterference
  
  calculateQuality()
}

window.qualityAssessmentRef = {
  calculateQuality
}
</script>

<style scoped>
.quality-assessment-card {
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

.header-actions {
  display: flex;
  gap: 12px;
}

.action-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
}

.action-btn.primary {
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  color: #0a0e14;
}

.action-btn.primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0, 255, 136, 0.3);
}

.action-btn.secondary {
  background: rgba(31, 41, 55, 0.6);
  color: #e0e6ed;
  border: 1px solid #1f2937;
}

.action-btn.secondary:hover {
  background: rgba(31, 41, 55, 0.9);
  border-color: #374151;
}

.assessment-result {
  margin-top: 24px;
}

.result-section {
  margin-bottom: 28px;
}

.result-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
}

.section-icon {
  font-size: 20px;
}

.section-title {
  font-size: 16px;
  font-weight: 600;
}

.score-container {
  display: flex;
  align-items: center;
  gap: 40px;
  padding: 24px;
  background: rgba(31, 41, 55, 0.4);
  border-radius: 12px;
}

.score-circle {
  position: relative;
  flex-shrink: 0;
}

.progress-ring {
  transform: rotate(-90deg);
}

.progress-ring-circle-bg {
  fill: none;
  stroke: #1f2937;
  stroke-width: 8;
}

.progress-ring-circle {
  fill: none;
  stroke-width: 8;
  stroke-linecap: round;
  transition: stroke-dashoffset 1s ease;
}

.score-inner {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}

.score-value {
  font-size: 48px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  background: linear-gradient(90deg, #00ff88 0%, #00d4ff 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.score-unit {
  font-size: 18px;
  color: #6b7280;
}

.score-info {
  flex: 1;
}

.score-info h3 {
  font-size: 22px;
  font-weight: 700;
  margin: 0 0 16px 0;
}

.score-level-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 600;
}

.score-level-badge.level-优秀 {
  background: rgba(0, 255, 136, 0.15);
  color: #00ff88;
  border: 1px solid rgba(0, 255, 136, 0.3);
}

.score-level-badge.level-良好 {
  background: rgba(255, 165, 0, 0.15);
  color: #ffa500;
  border: 1px solid rgba(255, 165, 0, 0.3);
}

.score-level-badge.level-较差 {
  background: rgba(255, 107, 107, 0.15);
  color: #ff6b6b;
  border: 1px solid rgba(255, 107, 107, 0.3);
}

.level-icon {
  font-size: 18px;
}

.suggestions-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.suggestion-item {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 14px 16px;
  background: rgba(31, 41, 55, 0.4);
  border-radius: 10px;
}

.suggestion-icon {
  font-size: 18px;
  margin-top: 2px;
  flex-shrink: 0;
}

.suggestion-text {
  font-size: 14px;
  color: #d1d5db;
  line-height: 1.5;
}

.comparison-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.comparison-item {
  background: rgba(31, 41, 55, 0.4);
  border-radius: 10px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.comparison-label {
  font-size: 12px;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.comparison-values {
  display: flex;
  align-items: center;
  gap: 12px;
}

.value-column {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.value-label {
  font-size: 11px;
  color: #6b7280;
}

.value-current {
  font-size: 14px;
  font-weight: 500;
}

.value-recommended {
  font-size: 14px;
  font-weight: 600;
  color: #00ff88;
}

.value-divider {
  font-size: 16px;
  color: #374151;
}

.comparison-status {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 600;
}

.comparison-status.success {
  background: rgba(0, 255, 136, 0.1);
  color: #00ff88;
}

.comparison-status.warning {
  background: rgba(255, 165, 0, 0.1);
  color: #ffa500;
}

.status-icon {
  font-size: 14px;
}
</style>
