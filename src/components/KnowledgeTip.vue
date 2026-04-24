<template>
  <el-card class="knowledge-tip-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>知识小贴士</span>
      </div>
    </template>
    <div class="tip-content">
      <el-alert
        :title="tipTitle"
        :description="tipContent"
        type="info"
        :closable="false"
        show-icon
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

const tipTitle = ref('💡 小贴士')
const tipContent = ref('选择参数和环境以获取相关知识')

const updateTip = () => {
  if (!props.paramConfigRef || !props.environmentSelectRef) return

  const params = props.paramConfigRef.getParams()
  const environment = props.environmentSelectRef.getEnvironment()

  if (environment === 'mountain' && params.frequency === 'hf') {
    tipContent.value = '在山地环境中，HF频段通过电离层反射实现超视距传输，能够跨越山脊障碍。'
  } else if (environment === 'city' && params.frequency === 'uhf') {
    tipContent.value = '在城市环境中，UHF频段因其较强的穿透能力而优于VHF和HF，能够更好地穿透建筑物。'
  } else if (environment === 'interference' && params.encryption === 'frequency-hopping') {
    tipContent.value = '在强电磁干扰环境中，跳频技术能够使敌方无法锁定频率，有效提高抗干扰能力。'
  } else if (params.encryption === 'spread-spectrum') {
    tipContent.value = '扩频技术将信号扩展到更宽的频带，使信号"淹没"在噪声中，具有低截获概率。'
  } else if (params.antiInterference === 'adaptive-fm') {
    tipContent.value = '自适应调频技术能够根据环境自动调整频率，有效抵抗敌方干扰。'
  } else if (params.power < 3) {
    tipContent.value = '低功率设置虽然能耗小，但通信距离受限，适合短距离通信。'
  } else if (params.power > 8) {
    tipContent.value = '高功率设置通信距离远，但能耗较大，需要权衡续航能力。'
  } else {
    tipContent.value = '根据环境和任务需求选择合适的通信参数，是确保通信质量的关键。'
  }
}

// 监听参数和环境变化
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
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.tip-content {
  margin-top: 10px;
}
</style>