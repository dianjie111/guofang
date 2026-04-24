<template>
  <el-card class="quality-assessment-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>通信质量评估</span>
        <el-button type="primary" @click="calculateQuality">计算评估</el-button>
        <el-button type="success" size="small" @click="applyRecommendedConfig">智能优化</el-button>
      </div>
    </template>
    <div class="assessment-result" v-if="showResult">
      <el-divider>评估结果</el-divider>
      <div class="score-container">
        <div class="score-circle">
          <el-progress type="circle" :percentage="score" :color="scoreColor" :width="150"></el-progress>
        </div>
        <div class="score-info">
          <h3>通信质量评分</h3>
          <p class="score-value">{{ score }}/100</p>
          <p class="score-level">{{ scoreLevel }}</p>
        </div>
      </div>
      <div class="suggestions">
        <h4>建议</h4>
        <el-list>
          <el-list-item v-for="(suggestion, index) in suggestions" :key="index">
            {{ suggestion }}
          </el-list-item>
        </el-list>
      </div>
      <div class="config-comparison" v-if="showComparison">
        <el-divider>配置对比</el-divider>
        <el-table :data="comparisonData" style="width: 100%">
          <el-table-column prop="param" label="参数" width="120"></el-table-column>
          <el-table-column prop="current" label="当前配置" width="150"></el-table-column>
          <el-table-column prop="recommended" label="推荐配置" width="150"></el-table-column>
          <el-table-column prop="status" label="状态">
            <template #default="scope">
              <el-tag :type="scope.row.status.type">{{ scope.row.status.text }}</el-tag>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { ref, computed, defineProps, inject } from 'vue'
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

const scoreColor = computed(() => {
  if (score.value >= 80) return '#67C23A'
  if (score.value >= 60) return '#E6A23C'
  return '#F56C6C'
})

const scoreLevel = computed(() => {
  if (score.value >= 80) return '优秀'
  if (score.value >= 60) return '良好'
  return '较差'
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
  let baseScore = 30 // 降低基础分数，使评分更加严格
  
  if (window.currentTask) {
    // 获取任务的推荐配置
    const recommendedConfig = window.currentTask.recommendedConfig.reduce((acc, item) => {
      acc[item.param] = item.value
      return acc
    }, {})
    
    // 频段评分 - 更加严格
    if (params.frequency === 'hf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('HF')) {
      baseScore += 30
    } else if (params.frequency === 'uhf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('UHF')) {
      baseScore += 30
    } else if (params.frequency === 'vhf' && recommendedConfig['频率'] && recommendedConfig['频率'].includes('VHF')) {
      baseScore += 30
    } else {
      baseScore += 5 // 大幅降低不匹配的分数
    }
    
    // 加密方式评分 - 更加严格
    if (params.encryption === 'frequency-hopping' && recommendedConfig['加密方式'] && recommendedConfig['加密方式'].includes('跳频')) {
      baseScore += 25
    } else if (params.encryption === 'spread-spectrum' && recommendedConfig['加密方式'] && recommendedConfig['加密方式'].includes('扩频')) {
      baseScore += 25
    } else {
      baseScore += 5 // 大幅降低不匹配的分数
    }
    
    // 功率评分 - 更加严格
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
      baseScore += params.power * 1.5 // 降低基础功率分数
    }
    
    // 抗干扰模式评分 - 更加严格
    if (params.antiInterference === 'adaptive-fm' && recommendedConfig['抗干扰模式'] && recommendedConfig['抗干扰模式'].includes('自适应')) {
      baseScore += 20
    } else if (params.antiInterference === 'error-correction' && recommendedConfig['抗干扰模式'] && recommendedConfig['抗干扰模式'].includes('纠错')) {
      baseScore += 20
    } else {
      baseScore += 5 // 大幅降低不匹配的分数
    }
  } else {
    // 非任务模式下的评分（保持原有逻辑但更加严格）
    // 频段选择分数
    if (environment === 'mountain' && params.frequency === 'hf') {
      baseScore += 20
    } else if (environment === 'city' && params.frequency === 'uhf') {
      baseScore += 20
    } else if (environment === 'interference' && (params.frequency === 'vhf' || params.frequency === 'uhf')) {
      baseScore += 15
    } else {
      baseScore += 5
    }
    
    // 加密方式分数
    if (params.encryption === 'frequency-hopping') {
      baseScore += 15
    } else if (params.encryption === 'spread-spectrum') {
      baseScore += 10
    } else {
      baseScore += 5
    }
    
    // 功率分数
    baseScore += params.power * 1.5 // 降低基础功率分数
    
    // 抗干扰模式分数
    if (params.antiInterference === 'adaptive-fm') {
      baseScore += 10
    } else if (params.antiInterference === 'error-correction') {
      baseScore += 8
    }
  }
  
  // 环境特殊加分 - 更加严格
  if (environment === 'interference' && params.encryption === 'frequency-hopping') {
    baseScore += 10
  } else if (environment === 'city' && params.encryption === 'spread-spectrum') {
    baseScore += 8
  }
  
  // 确保分数在0-100之间
  score.value = Math.min(100, Math.max(0, baseScore))
  
  // 生成建议
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
  
  // 生成配置对比
  generateComparisonData(params, environment)
  
  // 添加到历史记录
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
  
  // 检查是否通过任务
  if (window.currentTask && score.value >= 70) {
    // 显示通过提示
    ElMessage({
      message: '任务通过！正在返回首页...',
      type: 'success',
      duration: 3000
    })
    
    // 获取当前任务的信息
    const taskEnvironment = window.currentTask.environment || 'mountain'
    const taskName = window.currentTask.title || '未知任务'
    
    // 延时3秒返回首页
    setTimeout(() => {
      if (window.completeCurrentTask) {
        window.completeCurrentTask()
      }
      // 更新最佳成绩
      if (window.updateBestScore) {
        window.updateBestScore(taskEnvironment, score.value)
      }
      // 添加训练成绩到报告
      if (window.addTrainingScore) {
        window.addTrainingScore(taskName, score.value)
      }
      // 切换到首页
      if (window.switchToHome) {
        window.switchToHome()
      }
    }, 3000)
  } else if (window.currentTask && score.value < 70) {
    // 显示未通过提示
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
        ? { type: 'success', text: '✅ 匹配' } 
        : { type: 'warning', text: '🔄 建议切换' }
    },
    {
      param: '加密方式',
      current: getEncryptionName(params.encryption),
      recommended: getEncryptionName(recommended.encryption),
      status: params.encryption === recommended.encryption 
        ? { type: 'success', text: '✅ 匹配' } 
        : { type: 'warning', text: '🔄 建议切换' }
    },
    {
      param: '功率',
      current: params.power,
      recommended: recommended.power,
      status: params.power === recommended.power 
        ? { type: 'success', text: '✅ 匹配' } 
        : { type: 'warning', text: '🔄 建议调整' }
    },
    {
      param: '抗干扰模式',
      current: getAntiInterferenceName(params.antiInterference),
      recommended: getAntiInterferenceName(recommended.antiInterference),
      status: params.antiInterference === recommended.antiInterference 
        ? { type: 'success', text: '✅ 匹配' } 
        : { type: 'warning', text: '🔄 建议切换' }
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
  
  // 应用推荐配置
  props.paramConfigRef.form.frequency = recommended.frequency
  props.paramConfigRef.form.encryption = recommended.encryption
  props.paramConfigRef.form.power = recommended.power
  props.paramConfigRef.form.antiInterference = recommended.antiInterference
  
  // 重新计算评估
  calculateQuality()
}

// 暴露给全局，以便其他组件调用
window.qualityAssessmentRef = {
  calculateQuality
}
</script>

<style scoped>
.quality-assessment-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.assessment-result {
  margin-top: 20px;
}

.score-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
  margin-bottom: 30px;
}

.score-circle {
  flex: 1;
  display: flex;
  justify-content: center;
}

.score-info {
  flex: 1;
  text-align: center;
}

.score-value {
  font-size: 32px;
  font-weight: bold;
  color: #1890ff;
  margin: 10px 0;
}

.score-level {
  font-size: 18px;
  color: #606266;
}

.suggestions {
  margin-top: 20px;
}

.config-comparison {
  margin-top: 30px;
}
</style>