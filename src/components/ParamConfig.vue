<template>
  <div class="param-config-page">
    <div class="page-header">
      <div class="header-badge">
        <span class="badge-icon">⚙️</span>
        <span class="badge-text">参数控制</span>
      </div>
      <h1 class="page-title">通信参数配置</h1>
      <p class="page-subtitle">调整各项参数以适应不同的作战环境</p>
    </div>

    <div class="config-grid">
      <!-- 频率参数 -->
      <div class="config-card">
        <div class="card-header">
          <div class="header-icon">📡</div>
          <div class="header-text">
            <h3 class="card-title">频率选择</h3>
            <p class="card-subtitle">选择合适的通信频段</p>
          </div>
        </div>
        <div class="card-body">
          <div class="option-grid">
            <button
              v-for="option in frequencyOptions"
              :key="option.value"
              class="option-button"
              :class="{ active: form.frequency === option.value }"
              @click="form.frequency = option.value"
            >
              <div class="option-icon">{{ option.icon }}</div>
              <div class="option-label">{{ option.label }}</div>
              <div class="option-desc">{{ option.shortDesc }}</div>
            </button>
          </div>
          <div class="hint-box" v-if="frequencyHint">
            <div class="hint-icon">ℹ️</div>
            <div class="hint-text">{{ frequencyHint.text }}</div>
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
        </div>
        <div class="card-body">
          <div class="option-grid">
            <button
              v-for="option in encryptionOptions"
              :key="option.value"
              class="option-button"
              :class="{ active: form.encryption === option.value }"
              @click="form.encryption = option.value"
            >
              <div class="option-icon">{{ option.icon }}</div>
              <div class="option-label">{{ option.label }}</div>
              <div class="option-desc">{{ option.shortDesc }}</div>
            </button>
          </div>
          <div class="hint-box" v-if="encryptionHint">
            <div class="hint-icon">💡</div>
            <div class="hint-text">{{ encryptionHint.text }}</div>
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
        </div>
        <div class="card-body">
          <div class="power-control">
            <div class="power-indicator">
              <div class="power-levels">
                <div
                  v-for="i in 10"
                  :key="i"
                  class="power-bar"
                  :class="{ active: i <= form.power, warning: i > 8, low: i <= 2 }"
                ></div>
              </div>
              <div class="power-value">
                <span class="power-number">{{ form.power }}</span>
                <span class="power-unit">/10</span>
              </div>
            </div>
            <input
              type="range"
              class="power-slider"
              :min="1"
              :max="10"
              :step="1"
              v-model="form.power"
            />
            <div class="power-labels">
              <span class="label-left">低功率</span>
              <span class="label-right">高功率</span>
            </div>
          </div>
          <div class="hint-box" v-if="powerHint">
            <div class="hint-icon">⚠️</div>
            <div class="hint-text">{{ powerHint.text }}</div>
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
        </div>
        <div class="card-body">
          <div class="option-grid">
            <button
              v-for="option in antiInterferenceOptions"
              :key="option.value"
              class="option-button"
              :class="{ active: form.antiInterference === option.value }"
              @click="form.antiInterference = option.value"
            >
              <div class="option-icon">{{ option.icon }}</div>
              <div class="option-label">{{ option.label }}</div>
              <div class="option-desc">{{ option.shortDesc }}</div>
            </button>
          </div>
          <div class="hint-box danger" v-if="antiInterferenceHint">
            <div class="hint-icon">🔴</div>
            <div class="hint-text">{{ antiInterferenceHint.text }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, defineExpose } from 'vue'

const form = ref({
  frequency: 'vhf',
  encryption: 'frequency-hopping',
  power: 5,
  antiInterference: 'adaptive-fm'
})

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

const frequencyHint = computed(() => {
  switch (form.value.frequency) {
    case 'hf':
      return { text: 'HF频段：通过电离层反射实现超视距传输，适合山地和远距离通信，但受太阳活动影响较大' }
    case 'vhf':
      return { text: 'VHF频段：视距传播，抗干扰能力较好，适合近距离分队通信，但易受地形遮挡' }
    case 'uhf':
      return { text: 'UHF频段：穿透能力强，带宽大、数据传输快，适合城市环境和复杂地形' }
    default:
      return null
  }
})

const encryptionHint = computed(() => {
  switch (form.value.encryption) {
    case 'frequency-hopping':
      return { text: '跳频技术：通信双方按照伪随机序列同步改变载波频率，每秒跳变可达20000跳以上，抗干扰能力强' }
    case 'spread-spectrum':
      return { text: '扩频技术：将信号扩展到更宽的频带，使信号"淹没"在噪声中，低截获概率，适合隐蔽通信' }
    case 'normal':
      return { text: '常规加密：基础加密方式，抗干扰能力较弱，适合低威胁环境' }
    default:
      return null
  }
})

const powerHint = computed(() => {
  if (form.value.power < 3) {
    return { text: '低功率模式：通信距离受限，但能耗低、隐蔽性好，适合近距离隐蔽通信' }
  } else if (form.value.power > 8) {
    return { text: '高功率模式：通信距离远，但能耗较大、易被敌方探测，适合紧急远距离通信' }
  } else {
    return { text: '适中功率：平衡通信距离和能耗，适合大多数常规作战场景' }
  }
})

const antiInterferenceHint = computed(() => {
  switch (form.value.antiInterference) {
    case 'adaptive-fm':
      return { text: '自适应调频：实时监测干扰并自动调整工作频率，抗干扰能力强，适合复杂电磁环境' }
    case 'error-correction':
      return { text: '纠错编码：通过冗余编码检测和纠正传输错误，提高数据传输可靠性' }
    case 'none':
      return { text: '无抗干扰保护：通信系统易受干扰，仅适合电磁环境干净的区域' }
    default:
      return null
  }
})

defineExpose({
  getParams: () => form.value,
  form
})
</script>

<style scoped>
.param-config-page {
  padding: 0;
}

.page-header {
  margin-bottom: 32px;
  text-align: left;
}

.header-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 14px;
  background: rgba(0, 212, 255, 0.1);
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 50px;
  margin-bottom: 16px;
}

.badge-icon {
  color: #00d4ff;
  font-size: 14px;
}

.badge-text {
  font-size: 11px;
  color: #00d4ff;
  letter-spacing: 2px;
  font-weight: 600;
}

.page-title {
  font-size: 32px;
  font-weight: 800;
  margin: 0 0 8px 0;
  letter-spacing: 1px;
}

.page-subtitle {
  font-size: 14px;
  color: #6b7280;
  margin: 0;
}

.config-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.config-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
}

.config-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent 0%, #00d4ff 30%, #00ff88 70%, transparent 100%);
  opacity: 0.5;
}

.config-card:hover {
  border-color: #374151;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3);
}

.config-card.full-width {
  grid-column: 1 / -1;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 20px;
}

.header-icon {
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.15) 0%, rgba(0, 255, 136, 0.15) 100%);
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

.card-body {
  position: relative;
  z-index: 1;
}

.option-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-bottom: 16px;
}

.option-button {
  background: rgba(31, 41, 55, 0.5);
  border: 2px solid #1f2937;
  border-radius: 12px;
  padding: 16px 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.option-button:hover {
  background: rgba(31, 41, 55, 0.8);
  border-color: #374151;
  transform: translateY(-2px);
}

.option-button.active {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-color: #00ff88;
  box-shadow: 0 4px 20px rgba(0, 255, 136, 0.2);
}

.option-icon {
  font-size: 28px;
}

.option-label {
  font-size: 13px;
  font-weight: 600;
}

.option-desc {
  font-size: 11px;
  color: #6b7280;
}

.power-control {
  margin-bottom: 16px;
}

.power-indicator {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 20px;
}

.power-levels {
  display: flex;
  gap: 4px;
  flex: 1;
}

.power-bar {
  flex: 1;
  height: 40px;
  background: rgba(31, 41, 55, 0.5);
  border-radius: 6px;
  transition: all 0.3s ease;
}

.power-bar.active {
  background: linear-gradient(180deg, #00ff88 0%, #00cc6a 100%);
  box-shadow: 0 0 10px rgba(0, 255, 136, 0.3);
}

.power-bar.active.warning {
  background: linear-gradient(180deg, #ffa500 0%, #cc8400 100%);
  box-shadow: 0 0 10px rgba(255, 165, 0, 0.3);
}

.power-bar.active.low {
  background: linear-gradient(180deg, #00d4ff 0%, #0099cc 100%);
  box-shadow: 0 0 10px rgba(0, 212, 255, 0.3);
}

.power-value {
  display: flex;
  align-items: baseline;
  gap: 4px;
  padding: 8px 16px;
  background: rgba(0, 255, 136, 0.1);
  border-radius: 10px;
  font-family: 'Courier New', monospace;
}

.power-number {
  font-size: 32px;
  font-weight: 800;
  color: #00ff88;
}

.power-unit {
  font-size: 14px;
  color: #6b7280;
}

.power-slider {
  width: 100%;
  height: 8px;
  background: #1f2937;
  border-radius: 10px;
  outline: none;
  -webkit-appearance: none;
  cursor: pointer;
}

.power-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 24px;
  height: 24px;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 255, 136, 0.4);
}

.power-labels {
  display: flex;
  justify-content: space-between;
  margin-top: 8px;
  font-size: 12px;
  color: #6b7280;
}

.hint-box {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  padding: 14px 16px;
  background: rgba(0, 212, 255, 0.08);
  border: 1px solid rgba(0, 212, 255, 0.15);
  border-radius: 10px;
}

.hint-box.danger {
  background: rgba(255, 107, 107, 0.08);
  border-color: rgba(255, 107, 107, 0.15);
}

.hint-icon {
  font-size: 16px;
  margin-top: 2px;
  flex-shrink: 0;
}

.hint-text {
  font-size: 13px;
  color: #d1d5db;
  line-height: 1.6;
}
</style>