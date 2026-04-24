<template>
  <div class="history-record-card">
    <div class="card-header">
      <div class="header-icon">📋</div>
      <div class="header-text">
        <h3 class="card-title">历史配置记录</h3>
        <p class="card-subtitle">查看最近的参数配置</p>
      </div>
      <button class="clear-btn" @click="clearHistory">
        <span class="btn-icon">🗑️</span>
        <span>清空记录</span>
      </button>
    </div>
    <div class="history-content">
      <div v-if="historyList.length > 0" class="history-grid">
        <div 
          v-for="(record, index) in historyList" 
          :key="index"
          class="history-item"
        >
          <div class="history-header">
            <span class="history-time">{{ record.time }}</span>
            <div class="history-badges">
              <span :class="['task-badge', record.taskId ? 'has-task' : 'no-task']">
                {{ record.taskId ? `任务 ${record.taskId}` : '自由配置' }}
              </span>
              <span :class="['env-badge', record.environment]">
                {{ getEnvironmentName(record.environment) }}
              </span>
            </div>
          </div>
          <div class="history-details">
            <div class="detail-item">
              <span class="detail-label">频率</span>
              <span class="detail-value">{{ record.frequency }}</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">加密</span>
              <span class="detail-value">{{ record.encryption }}</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">功率</span>
              <span class="detail-value">{{ record.power }}</span>
            </div>
          </div>
          <div class="history-score">
            <div class="score-header">
              <span class="score-label">质量分</span>
              <span class="score-value">{{ record.score }}</span>
            </div>
            <div class="score-bar">
              <div 
                class="score-fill" 
                :style="{ 
                  width: `${record.score}%`,
                  background: `linear-gradient(90deg, ${getScoreColor(record.score)} 0%, ${getScoreColor(record.score)}cc 100%)`
                }"
              ></div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="empty-history">
        <div class="empty-icon">📭</div>
        <div class="empty-text">暂无历史记录</div>
      </div>
    </div>
  </div>
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

.clear-btn {
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
  background: rgba(255, 107, 107, 0.1);
  color: #ff6b6b;
}

.clear-btn:hover {
  background: rgba(255, 107, 107, 0.2);
}

.history-content {
  margin-top: 10px;
}

.history-grid {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.history-item {
  background: rgba(31, 41, 55, 0.4);
  border-radius: 12px;
  padding: 18px;
  border: 1px solid rgba(31, 41, 55, 0.6);
  transition: all 0.3s ease;
}

.history-item:hover {
  border-color: #374151;
  transform: translateY(-2px);
}

.history-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 14px;
}

.history-time {
  font-size: 13px;
  color: #6b7280;
  font-family: 'Courier New', monospace;
}

.history-badges {
  display: flex;
  gap: 8px;
}

.task-badge,
.env-badge {
  padding: 4px 10px;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 600;
}

.task-badge.has-task {
  background: rgba(0, 212, 255, 0.15);
  color: #00d4ff;
}

.task-badge.no-task {
  background: rgba(107, 114, 128, 0.2);
  color: #9ca3af;
}

.env-badge.mountain {
  background: rgba(0, 255, 136, 0.15);
  color: #00ff88;
}

.env-badge.city {
  background: rgba(255, 165, 0, 0.15);
  color: #ffa500;
}

.env-badge.interference {
  background: rgba(255, 107, 107, 0.15);
  color: #ff6b6b;
}

.history-details {
  display: flex;
  gap: 24px;
  margin-bottom: 14px;
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.detail-label {
  font-size: 11px;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.detail-value {
  font-size: 14px;
  font-weight: 600;
  color: #e0e6ed;
}

.history-score {
  padding-top: 14px;
  border-top: 1px solid rgba(31, 41, 55, 0.8);
}

.score-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.score-label {
  font-size: 12px;
  color: #6b7280;
  font-weight: 600;
}

.score-value {
  font-size: 18px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
}

.score-bar {
  width: 100%;
  height: 8px;
  background: #1f2937;
  border-radius: 4px;
  overflow: hidden;
}

.score-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 0.5s ease;
}

.empty-history {
  text-align: center;
  padding: 48px 24px;
}

.empty-icon {
  font-size: 64px;
  margin-bottom: 16px;
  opacity: 0.5;
}

.empty-text {
  font-size: 16px;
  color: #6b7280;
}
</style>
