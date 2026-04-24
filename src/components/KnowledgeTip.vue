<template>
  <el-card class="knowledge-tip-card" :body-style="{ padding: '24px' }">
    <template #header>
      <div class="card-header">
        <div class="header-icon">💡</div>
        <div class="header-text">
          <h3 class="card-title">知识小贴士</h3>
          <p class="card-subtitle">根据配置获取相关知识</p>
        </div>
      </div>
    </template>
    <div class="tip-content">
      <el-alert
        :title="tipTitle"
        :description="tipContent"
        type="info"
        :closable="false"
        show-icon
        class="tip-box"
      />
    </div>
  </el-card>
</template>

<script setup>
import { ref, computed, defineProps, watch } from 'vue'

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

const tipTitle = ref('小提示')
const tipContent = ref('选择参数和环境以获取相关知识')

const updateTip = () => {
  if (!props.paramConfigRef || !props.environmentSelectRef) return

  const params = props.paramConfigRef.getParams()
  const environment = props.environmentSelectRef.getEnvironment()

  if (environment === 'mountain' && params.frequency === 'hf') {
    tipTitle.value = '山地HF通信'
    tipContent.value = '在山地环境中，HF频段通过电离层反射实现超视距传输，能够跨越山脊障碍。'
  } else if (environment === 'city' && params.frequency === 'uhf') {
    tipTitle.value = '城市UHF通信'
    tipContent.value = '在城市环境中，UHF频段因其较强的穿透能力而优于VHF和HF，能够更好地穿透建筑物。'
  } else if (environment === 'interference' && params.encryption === 'frequency-hopping') {
    tipTitle.value = '跳频抗干扰'
    tipContent.value = '在强电磁干扰环境中，跳频技术能够使敌方无法锁定频率，有效提高抗干扰能力。'
  } else if (params.encryption === 'spread-spectrum') {
    tipTitle.value = '扩频技术'
    tipContent.value = '扩频技术将信号扩展到更宽的频带，使信号"淹没"在噪声中，具有低截获概率。'
  } else if (params.antiInterference === 'adaptive-fm') {
    tipTitle.value = '自适应调频'
    tipContent.value = '自适应调频技术能够根据环境自动调整频率，有效抵抗敌方干扰。'
  } else if (params.power < 3) {
    tipTitle.value = '低功率设置'
    tipContent.value = '低功率设置虽然能耗小，但通信距离受限，适合短距离通信。'
  } else if (params.power > 8) {
    tipTitle.value = '高功率设置'
    tipContent.value = '高功率设置通信距离远，但能耗较大，需要权衡续航能力。'
  } else {
    tipTitle.value = '配置建议'
    tipContent.value = '根据环境和任务需求选择合适的通信参数，是确保通信质量的关键。'
  }
}

watch(
  () => props.paramConfigRef?.getParams(),
  () => updateTip(),
  { deep: true }
)

watch(
  () => props.environmentSelectRef?.getEnvironment(),
  () => updateTip()
)
</script>

<style scoped>
.knowledge-tip-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  position: relative;
  overflow: hidden;
  margin-bottom: 20px;
}

.knowledge-tip-card::before {
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

.tip-content {
  margin-top: 24px;
}

.tip-box {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.1) 0%, rgba(0, 255, 136, 0.05) 100%);
  border-color: rgba(0, 212, 255, 0.2);
  border-radius: 12px;
}

.tip-box :deep(.el-alert__title) {
  color: #00d4ff;
  font-weight: 600;
  font-size: 16px;
}

.tip-box :deep(.el-alert__description) {
  color: #d1d5db;
  font-size: 14px;
  line-height: 1.6;
  margin-top: 8px;
}
</style>
