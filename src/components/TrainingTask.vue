<template>
  <div class="training-task-page">
    <!-- 页面头部 -->
    <div class="page-header" v-if="!currentTask">
      <div class="header-content">
        <div class="header-badge">
          <span class="badge-icon">⚔</span>
          <span class="badge-text">作战任务</span>
        </div>
        <h1 class="page-title">战术任务中心</h1>
        <p class="page-subtitle">选择任务并完成通信参数配置训练</p>
      </div>
    </div>
    
    <!-- 任务详情返回按钮 -->
    <div class="page-header compact" v-else>
      <button class="back-button" @click="backToTaskList">
        <span class="back-icon">←</span>
        <span class="back-text">返回任务列表</span>
      </button>
    </div>
    
    <!-- 任务列表 -->
    <div class="task-list-container" v-if="!currentTask">
      <div class="task-grid">
        <div 
          v-for="task in tasks" 
          :key="task.id"
          class="task-card"
          :class="{ 
            'task-completed': task.completed, 
            'task-in-progress': task.inProgress 
          }"
        >
          <div class="task-card-header">
            <div class="task-id-badge">{{ String(task.id).padStart(2, '0') }}</div>
            <div class="task-status" :class="'status-' + task.statusType">
              <span class="status-dot"></span>
              <span class="status-text">{{ task.statusText }}</span>
            </div>
          </div>
          
          <div class="task-card-body">
            <h3 class="task-title">{{ task.title }}</h3>
            <p class="task-description">{{ task.description }}</p>
            
            <div class="task-meta">
              <div class="meta-item">
                <span class="meta-icon">🎯</span>
                <span class="meta-text">{{ task.target }}</span>
              </div>
              <div class="meta-item">
                <span class="meta-icon">💡</span>
                <span class="meta-text">{{ task.hint }}</span>
              </div>
            </div>
          </div>
          
          <div class="task-card-footer">
            <button 
              v-if="!task.completed && !task.inProgress"
              class="action-button primary"
              @click="startTask(task.id)"
            >
              <span class="button-text">开始任务</span>
              <span class="button-arrow">→</span>
            </button>
            <button 
              v-else-if="task.inProgress"
              class="action-button success"
              @click="viewTask(task.id)"
            >
              <span class="button-text">继续任务</span>
              <span class="button-arrow">→</span>
            </button>
            <button 
              v-else
              class="action-button secondary"
              @click="viewTask(task.id)"
            >
              <span class="button-text">查看详情</span>
              <span class="button-arrow">→</span>
            </button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 任务详情 -->
    <div class="task-detail-container" v-if="currentTask">
      <div class="detail-main">
        <div class="detail-header-section">
          <div class="detail-id-badge">任务 {{ String(currentTask.id).padStart(2, '0') }}</div>
          <h2 class="detail-title">{{ currentTask.title }}</h2>
          <div class="detail-status" :class="'status-' + currentTask.statusType">
            <span class="status-dot"></span>
            <span class="status-text">{{ currentTask.statusText }}</span>
          </div>
        </div>
        
        <div class="detail-content">
          <div class="info-section">
            <div class="section-title">
              <span class="title-icon">📋</span>
              <span>任务描述</span>
            </div>
            <p class="section-text">{{ currentTask.description }}</p>
          </div>
          
          <div class="info-section">
            <div class="section-title">
              <span class="title-icon">🎯</span>
              <span>任务目标</span>
            </div>
            <p class="section-text">{{ currentTask.target }}</p>
          </div>
          
          <div class="info-section">
            <div class="section-title">
              <span class="title-icon">💡</span>
              <span>任务提示</span>
            </div>
            <p class="section-text">{{ currentTask.hint }}</p>
          </div>
          
          <div class="info-section">
            <div class="section-title">
              <span class="title-icon">⚙️</span>
              <span>推荐配置</span>
            </div>
            <div class="config-grid">
              <div v-for="config in currentTask.recommendedConfig" :key="config.param" class="config-item">
                <div class="config-label">{{ config.param }}</div>
                <div class="config-value">{{ config.value }}</div>
              </div>
            </div>
          </div>
        </div>
        
        <div class="detail-actions">
          <button class="main-action-button" @click="navigateToParamConfig">
            <span class="button-icon">🚀</span>
            <span class="button-text">前往参数配置</span>
          </button>
          <button v-if="currentTask.inProgress" class="complete-button" @click="completeCurrentTask">
            <span class="button-icon">✓</span>
            <span class="button-text">标记完成</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// 从localStorage读取任务完成情况
const loadTasksFromStorage = () => {
  try {
    const storedTasks = localStorage.getItem('trainingTasks')
    if (storedTasks) {
      const savedTasks = JSON.parse(storedTasks)
      // 合并保存的任务状态
      tasks.value.forEach(task => {
        const savedTask = savedTasks.find(t => t.id === task.id)
        if (savedTask) {
          task.completed = savedTask.completed
          task.inProgress = false // 确保刷新后不会保持进行中状态
          task.statusType = savedTask.completed ? 'success' : 'default'
          task.statusText = savedTask.completed ? '已完成' : '未完成'
        }
      })
      // 排序任务：未完成的任务在前，已完成的任务在后
      tasks.value.sort((a, b) => {
        if (a.completed && !b.completed) return 1
        if (!a.completed && b.completed) return -1
        return 0
      })
    }
  } catch (error) {
    console.error('Failed to load tasks from storage:', error)
  }
}

// 保存任务完成情况到localStorage
const saveTasksToStorage = () => {
  try {
    const tasksToSave = tasks.value.map(task => ({
      id: task.id,
      completed: task.completed
    }))
    localStorage.setItem('trainingTasks', JSON.stringify(tasksToSave))
  } catch (error) {
    console.error('Failed to save tasks to storage:', error)
  }
}

const tasks = ref([
  {
    id: 1,
    title: '山地远距离通信',
    description: '在山地环境中，实现远距离通信。山地地形复杂，信号遮挡严重，需要选择合适的频段和功率配置。',
    target: '通信质量达到80分以上',
    hint: '建议使用HF频段，适当提高功率，以利用电离层反射实现超视距传输。',
    recommendedConfig: [
      { param: '频率', value: 'HF (高频)' },
      { param: '加密方式', value: '跳频' },
      { param: '功率', value: '7-8' },
      { param: '抗干扰模式', value: '自适应调频' }
    ],
    environment: 'mountain',
    completed: false,
    inProgress: false,
    statusType: 'default',
    statusText: '未完成'
  },
  {
    id: 2,
    title: '抗干扰通信演练',
    description: '在强电磁干扰环境中保持通信。敌方会对通信频段进行干扰，需要使用抗干扰技术确保通信畅通。',
    target: '通信质量达到75分以上',
    hint: '建议使用跳频加密和自适应调频，以提高抗干扰能力。',
    recommendedConfig: [
      { param: '频率', value: 'VHF (甚高频)' },
      { param: '加密方式', value: '跳频' },
      { param: '功率', value: '8-9' },
      { param: '抗干扰模式', value: '自适应调频' }
    ],
    environment: 'interference',
    completed: false,
    inProgress: false,
    statusType: 'default',
    statusText: '未完成'
  },
  {
    id: 3,
    title: '城市环境通信',
    description: '在城市环境中实现稳定通信。城市建筑物密集，信号反射多，干扰源多，需要选择合适的频段和抗干扰技术。',
    target: '通信质量达到85分以上',
    hint: '建议使用UHF频段和扩频技术，以提高穿透能力和抗多径干扰能力。',
    recommendedConfig: [
      { param: '频率', value: 'UHF (特高频)' },
      { param: '加密方式', value: '扩频' },
      { param: '功率', value: '5-6' },
      { param: '抗干扰模式', value: '纠错编码' }
    ],
    environment: 'city',
    completed: false,
    inProgress: false,
    statusType: 'default',
    statusText: '未完成'
  },
  {
    id: 4,
    title: '隐蔽通信任务',
    description: '执行侦察任务，需隐蔽通信。需要使用低截获概率技术，避免被敌方探测到通信信号。',
    target: '通信质量达到70分以上',
    hint: '建议使用扩频技术，降低功率，以减少被敌方截获的概率。',
    recommendedConfig: [
      { param: '频率', value: 'UHF (特高频)' },
      { param: '加密方式', value: '扩频' },
      { param: '功率', value: '2-3' },
      { param: '抗干扰模式', value: '纠错编码' }
    ],
    environment: 'city',
    completed: false,
    inProgress: false,
    statusType: 'default',
    statusText: '未完成'
  }
])

const currentTask = ref(null)

const startTask = (taskId) => {
  const task = tasks.value.find(t => t.id === taskId)
  if (task) {
    task.inProgress = true
    task.statusType = 'warning'
    task.statusText = '进行中'
    currentTask.value = task
    window.currentTask = task
    window.currentTaskId = taskId
    if (window.hideSidebar) {
      window.hideSidebar()
    }
  }
}

const viewTask = (taskId) => {
  const task = tasks.value.find(t => t.id === taskId)
  if (task) {
    currentTask.value = task
    if (window.hideSidebar) {
      window.hideSidebar()
    }
  }
}

const backToTaskList = () => {
  if (currentTask.value) {
    const task = tasks.value.find(t => t.id === currentTask.value.id)
    if (task && !task.completed) {
      task.inProgress = false
      task.statusType = 'default'
      task.statusText = '未完成'
    }
  }
  window.currentTask = null
  window.currentTaskId = null
  currentTask.value = null
  if (window.showSidebar) {
    window.showSidebar()
  }
}

const completeCurrentTask = () => {
  if (currentTask.value) {
    const task = tasks.value.find(t => t.id === currentTask.value.id)
    if (task) {
      task.completed = true
      task.inProgress = false
      task.statusType = 'success'
      task.statusText = '已完成'
    }
  }
  tasks.value.sort((a, b) => {
    if (a.completed && !b.completed) return 1
    if (!a.completed && b.completed) return -1
    return 0
  })
  const completedCount = tasks.value.filter(t => t.completed).length
  if (window.updateTaskStats) {
    window.updateTaskStats(completedCount)
  }
  saveTasksToStorage()
  backToTaskList()
}

window.completeCurrentTask = completeCurrentTask

const navigateToParamConfig = () => {
  if (window.switchToParamConfig && currentTask.value) {
    window.switchToParamConfig(currentTask.value.environment)
  }
}

onMounted(() => {
  loadTasksFromStorage()
})

</script>

<style scoped>
.training-task-page {
  padding: 0;
}

/* 页面头部 */
.page-header {
  margin-bottom: 40px;
}

.page-header.compact {
  margin-bottom: 30px;
}

.header-content {
  text-align: left;
}

.header-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 14px;
  background: rgba(255, 165, 0, 0.1);
  border: 1px solid rgba(255, 165, 0, 0.2);
  border-radius: 50px;
  margin-bottom: 16px;
}

.badge-icon {
  color: #ffa500;
  font-size: 14px;
}

.badge-text {
  font-size: 11px;
  color: #ffa500;
  letter-spacing: 2px;
  font-weight: 600;
}

.page-title {
  font-size: 36px;
  font-weight: 800;
  margin: 0 0 8px 0;
  letter-spacing: 2px;
}

.page-subtitle {
  font-size: 15px;
  color: #6b7280;
  margin: 0;
}

.back-button {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: rgba(31, 41, 55, 0.5);
  border: 1px solid #1f2937;
  border-radius: 10px;
  color: #e0e6ed;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.back-button:hover {
  background: rgba(31, 41, 55, 0.8);
  border-color: #374151;
}

.back-icon {
  font-size: 18px;
}

/* 任务列表 */
.task-list-container {
  max-width: 1200px;
}

.task-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;
}

.task-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
}

.task-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: linear-gradient(180deg, #00ff88 0%, #00d4ff 100%);
  opacity: 0.3;
  transition: opacity 0.3s ease;
}

.task-card:hover {
  transform: translateY(-4px);
  border-color: #374151;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.3);
}

.task-card:hover::before {
  opacity: 0.6;
}

.task-card.task-completed {
  opacity: 0.7;
}

.task-card.task-completed::before {
  background: #00ff88;
}

.task-card.task-in-progress::before {
  background: #ffa500;
  animation: pulse-border 2s ease-in-out infinite;
}

@keyframes pulse-border {
  0%, 100% { opacity: 0.4; }
  50% { opacity: 0.8; }
}

.task-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.task-id-badge {
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  color: #00ff88;
}

.task-status {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 4px 12px;
  border-radius: 50px;
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 1px;
}

.status-default {
  background: rgba(107, 114, 128, 0.1);
  color: #6b7280;
}

.status-success {
  background: rgba(0, 255, 136, 0.15);
  color: #00ff88;
}

.status-warning {
  background: rgba(255, 165, 0, 0.15);
  color: #ffa500;
}

.status-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: currentColor;
}

.status-warning .status-dot {
  animation: blink 1.5s ease-in-out infinite;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.3; }
}

.task-card-body {
  flex: 1;
  margin-bottom: 20px;
}

.task-title {
  font-size: 18px;
  font-weight: 700;
  margin: 0 0 10px 0;
}

.task-description {
  font-size: 13px;
  color: #9ca3af;
  margin: 0 0 16px 0;
  line-height: 1.6;
}

.task-meta {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.meta-item {
  display: flex;
  align-items: flex-start;
  gap: 8px;
}

.meta-icon {
  font-size: 14px;
  margin-top: 1px;
}

.meta-text {
  font-size: 12px;
  color: #6b7280;
}

.task-card-footer {
  padding-top: 16px;
  border-top: 1px solid #1f2937;
}

.action-button {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 12px 20px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
}

.action-button.primary {
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  color: #0a0e14;
}

.action-button.primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0, 255, 136, 0.3);
}

.action-button.success {
  background: linear-gradient(135deg, #ffa500 0%, #cc8400 100%);
  color: #0a0e14;
}

.action-button.success:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(255, 165, 0, 0.3);
}

.action-button.secondary {
  background: rgba(31, 41, 55, 0.6);
  color: #e0e6ed;
  border: 1px solid #1f2937;
}

.action-button.secondary:hover {
  background: rgba(31, 41, 55, 0.9);
  border-color: #374151;
}

.button-arrow {
  transition: transform 0.3s ease;
}

.action-button:hover .button-arrow {
  transform: translateX(4px);
}

/* 任务详情 */
.task-detail-container {
  max-width: 900px;
}

.detail-main {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(10, 14, 20, 0.98) 100%);
  border: 1px solid #1f2937;
  border-radius: 20px;
  padding: 40px;
  position: relative;
  overflow: hidden;
}

.detail-main::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent 0%, #00ff88 20%, #00d4ff 50%, #00ff88 80%, transparent 100%);
}

.detail-header-section {
  margin-bottom: 32px;
  text-align: center;
}

.detail-id-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 56px;
  height: 56px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.2) 0%, rgba(0, 212, 255, 0.2) 100%);
  border-radius: 14px;
  font-size: 18px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  color: #00ff88;
  margin-bottom: 16px;
}

.detail-title {
  font-size: 28px;
  font-weight: 800;
  margin: 0 0 16px 0;
  letter-spacing: 1px;
}

.detail-status {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 20px;
  border-radius: 50px;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 1px;
}

.detail-content {
  margin-bottom: 36px;
}

.info-section {
  margin-bottom: 28px;
}

.section-title {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
  font-size: 14px;
  font-weight: 700;
  color: #9ca3af;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.title-icon {
  font-size: 16px;
}

.section-text {
  font-size: 14px;
  color: #d1d5db;
  line-height: 1.7;
  margin: 0;
  padding-left: 26px;
}

.config-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  padding-left: 26px;
}

.config-item {
  background: rgba(31, 41, 55, 0.5);
  border: 1px solid #1f2937;
  border-radius: 10px;
  padding: 14px 18px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.config-label {
  font-size: 13px;
  color: #6b7280;
  font-weight: 500;
}

.config-value {
  font-size: 14px;
  color: #00ff88;
  font-weight: 600;
  font-family: 'Courier New', monospace;
}

.detail-actions {
  display: flex;
  gap: 16px;
  padding-top: 28px;
  border-top: 1px solid #1f2937;
}

.main-action-button {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 16px 28px;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  border: none;
  border-radius: 12px;
  font-size: 15px;
  font-weight: 700;
  color: #0a0e14;
  cursor: pointer;
  transition: all 0.3s ease;
}

.main-action-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(0, 255, 136, 0.35);
}

.complete-button {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 16px 28px;
  background: rgba(0, 255, 136, 0.1);
  border: 1px solid rgba(0, 255, 136, 0.3);
  border-radius: 12px;
  font-size: 15px;
  font-weight: 600;
  color: #00ff88;
  cursor: pointer;
  transition: all 0.3s ease;
}

.complete-button:hover {
  background: rgba(0, 255, 136, 0.2);
  border-color: rgba(0, 255, 136, 0.5);
}

.button-icon {
  font-size: 18px;
}
</style>