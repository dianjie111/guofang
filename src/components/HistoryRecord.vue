<template>
  <el-card class="history-record-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>历史配置记录</span>
        <el-button type="primary" size="small" @click="clearHistory">清空记录</el-button>
      </div>
    </template>
    <div class="history-content">
      <el-table :data="historyList" style="width: 100%">
          <el-table-column prop="time" label="时间" width="180"></el-table-column>
          <el-table-column prop="taskId" label="任务" width="120">
            <template #default="scope">
              <el-tag v-if="scope.row.taskId" type="primary">任务 {{ scope.row.taskId }}</el-tag>
              <el-tag v-else type="info">自由配置</el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="environment" label="环境" width="120">
            <template #default="scope">
              <el-tag :type="getEnvironmentType(scope.row.environment)">
                {{ getEnvironmentName(scope.row.environment) }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="frequency" label="频率" width="100"></el-table-column>
          <el-table-column prop="encryption" label="加密方式" width="150"></el-table-column>
          <el-table-column prop="power" label="功率" width="80"></el-table-column>
          <el-table-column prop="score" label="质量分" width="100">
            <template #default="scope">
              <el-progress :percentage="scope.row.score" :color="getScoreColor(scope.row.score)" :stroke-width="10"></el-progress>
            </template>
          </el-table-column>
        </el-table>
      <div v-if="historyList.length === 0" class="empty-history">
        <el-empty description="暂无历史记录"></el-empty>
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { ref, defineExpose, onMounted } from 'vue'

const historyList = ref([])

// 从localStorage读取历史记录
const loadHistoryFromStorage = () => {
  try {
    const storedHistory = localStorage.getItem('communicationHistory')
    if (storedHistory) {
      historyList.value = JSON.parse(storedHistory)
    }
  } catch (error) {
    console.error('Failed to load history from storage:', error)
  }
}

// 保存历史记录到localStorage
const saveHistoryToStorage = () => {
  try {
    localStorage.setItem('communicationHistory', JSON.stringify(historyList.value))
  } catch (error) {
    console.error('Failed to save history to storage:', error)
  }
}

const addRecord = (record) => {
  const newRecord = {
    time: new Date().toLocaleString(),
    ...record
  }
  historyList.value.unshift(newRecord)
  // 只保留最近5条记录
  if (historyList.value.length > 5) {
    historyList.value = historyList.value.slice(0, 5)
  }
  // 保存到localStorage
  saveHistoryToStorage()
}

const clearHistory = () => {
  historyList.value = []
  // 保存到localStorage
  saveHistoryToStorage()
}

// 组件初始化时加载历史记录
onMounted(() => {
  loadHistoryFromStorage()
})

const getEnvironmentType = (environment) => {
  switch (environment) {
    case 'mountain': return 'success'
    case 'city': return 'warning'
    case 'interference': return 'danger'
    default: return 'info'
  }
}

const getEnvironmentName = (environment) => {
  switch (environment) {
    case 'mountain': return '山地'
    case 'city': return '城市'
    case 'interference': return '强电磁干扰'
    default: return environment
  }
}

const getScoreColor = (score) => {
  if (score >= 80) return '#67C23A'
  if (score >= 60) return '#E6A23C'
  return '#F56C6C'
}

defineExpose({
  addRecord,
  clearHistory
})
</script>

<style scoped>
.history-record-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.history-content {
  margin-top: 10px;
}

.empty-history {
  margin-top: 20px;
}
</style>