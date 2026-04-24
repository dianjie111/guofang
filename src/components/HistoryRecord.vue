<template>
  <el-card class="history-record-card" :body-style="{ padding: '24px' }">
    <template #header>
      <div class="card-header">
        <div class="header-icon">📋</div>
        <div class="header-text">
          <h3 class="card-title">历史配置记录</h3>
          <p class="card-subtitle">查看最近的参数配置</p>
        </div>
        <el-button type="danger" @click="clearHistory" class="clear-btn">
          <span class="btn-icon">🗑️</span>
          <span>清空记录</span>
        </el-button>
      </div>
    </template>
    <div class="history-content">
      <el-table 
        v-if="historyList.length > 0" 
        :data="historyList" 
        class="history-table"
        style="width: 100%"
      >
        <el-table-column prop="time" label="时间" width="180"></el-table-column>
        <el-table-column prop="taskId" label="任务" width="120">
          <template #default="scope">
            <el-tag :type="scope.row.taskId ? 'primary' : 'info'">
              {{ scope.row.taskId ? `任务 ${scope.row.taskId}` : '自由配置' }}
            </el-tag>
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
        <el-table-column prop="score" label="质量分" width="120">
          <template #default="scope">
            <el-progress 
              :percentage="scope.row.score" 
              :color="getScoreColor(scope.row.score)" 
              :stroke-width="10"
            />
          </template>
        </el-table-column>
      </el-table>
      <el-empty 
        v-else 
        description="暂无历史记录" 
        class="empty-history"
      />
    </div>
  </el-card>
</template>

<script setup>
import { ref, defineExpose, onMounted } from 'vue'

const historyList = ref([])

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
  if (historyList.value.length > 5) {
    historyList.value = historyList.value.slice(0, 5)
  }
  saveHistoryToStorage()
}

const clearHistory = () => {
  historyList.value = []
  saveHistoryToStorage()
}

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
  if (score >= 80) return '#00ff88'
  if (score >= 60) return '#ffa500'
  return '#ff6b6b'
}

defineExpose({
  addRecord,
  clearHistory
})
</script>

<style scoped>
.history-record-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  position: relative;
  overflow: hidden;
  margin-bottom: 20px;
}

.history-record-card::before {
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

.clear-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.3s ease;
  background: rgba(255, 107, 107, 0.1);
  color: #ff6b6b;
  border: 1px solid rgba(255, 107, 107, 0.2);
}

.clear-btn:hover {
  background: rgba(255, 107, 107, 0.2);
}

.history-content {
  margin-top: 24px;
}

.history-table {
  background: rgba(31, 41, 55, 0.4);
  border-radius: 12px;
  overflow: hidden;
}

.history-table :deep(.el-table__header-wrapper th) {
  background: rgba(31, 41, 55, 0.8);
  color: #e0e6ed;
  border-bottom: 1px solid #374151;
}

.history-table :deep(.el-table__body-wrapper tr) {
  background: transparent;
}

.history-table :deep(.el-table__body-wrapper tr:hover) {
  background: rgba(31, 41, 55, 0.6);
}

.history-table :deep(.el-table__body-wrapper td) {
  border-bottom: 1px solid rgba(31, 41, 55, 0.4);
  color: #e0e6ed;
}

.empty-history {
  padding: 48px 24px;
  text-align: center;
}
</style>
