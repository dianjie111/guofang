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
      
      <!-- 统计卡片 -->
      <div class="stats-cards">
        <div class="stat-card">
          <div class="stat-icon-wrapper">
            <span class="stat-icon">📋</span>
          </div>
          <div class="stat-content">
            <div class="stat-value">{{ tasks.length }}</div>
            <div class="stat-label">总任务</div>
          </div>
        </div>
        <div class="stat-card">
          <div class="stat-icon-wrapper accent">
            <span class="stat-icon">✅</span>
          </div>
          <div class="stat-content">
            <div class="stat-value">{{ completedCount }}</div>
            <div class="stat-label">已完成</div>
          </div>
        </div>
        <div class="stat-card">
          <div class="stat-icon-wrapper success">
            <span class="stat-icon">📊</span>
          </div>
          <div class="stat-content">
            <div class="stat-value">{{ completionRate }}%</div>
            <div class="stat-label">完成率</div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 任务详情返回按钮 -->
    <div class="page-header compact" v-else>
      <el-button type="info" @click="backToTaskList" class="back-button">
        <span class="back-icon">←</span>
        <span class="back-text">返回任务列表</span>
      </el-button>
    </div>
    
    <!-- 任务列表 -->
    <div class="task-list-container" v-if="!currentTask">
      <!-- 筛选标签 -->
      <div class="filter-tabs">
        <button 
          v-for="tab in filterTabs" 
          :key="tab.value"
          class="filter-tab"
          :class="{ active: activeFilter === tab.value }"
          @click="activeFilter = tab.value"
        >
          <span class="tab-icon">{{ tab.icon }}</span>
          <span class="tab-text">{{ tab.label }}</span>
        </button>
      </div>
      
      <!-- 任务网格 -->
      <div class="task-grid">
        <div 
          v-for="task in filteredTasks" 
          :key="task.id"
          class="task-item"
          :class="{ 
            'task-completed': task.completed, 
            'task-in-progress': task.inProgress 
          }"
        >
          <!-- 任务头部 -->
          <div class="task-header">
            <div class="task-id">
              <div class="id-badge">{{ String(task.id).padStart(2, '0') }}</div>
            </div>
            <el-tag :type="task.statusType" class="task-status">
              <span class="status-dot"></span>
              <span class="status-text">{{ task.statusText }}</span>
            </el-tag>
          </div>
          
          <!-- 任务内容 -->
          <div class="task-content">
            <div class="task-title-row">
              <div class="task-environment-icon" :class="'env-' + task.environment">
                <span class="env-icon">{{ getEnvironmentIcon(task.environment) }}</span>
              </div>
              <h3 class="task-title">{{ task.title }}</h3>
            </div>
            
            <p class="task-description">{{ task.description }}</p>
            
            <div class="task-target">
              <span class="target-icon">🎯</span>
              <span class="target-text">{{ task.target }}</span>
            </div>
            
            <div class="task-tags">
              <el-tag size="small" class="env-tag">{{ getEnvironmentName(task.environment) }}</el-tag>
              <el-tag size="small" class="difficulty-tag">{{ getDifficulty(task.id) }}</el-tag>
            </div>
          </div>
          
          <!-- 任务底部 -->
          <div class="task-footer">
            <el-button 
              v-if="!task.completed && !task.inProgress"
              type="primary" 
              class="action-btn start-btn"
              @click="startTask(task.id)"
            >
              <span class="btn-icon">🚀</span>
              <span class="btn-text">开始任务</span>
            </el-button>
            <el-button 
              v-else-if="task.inProgress"
              type="warning" 
              class="action-btn continue-btn"
              @click="viewTask(task.id)"
            >
              <span class="btn-icon">▶</span>
              <span class="btn-text">继续任务</span>
            </el-button>
            <el-button 
              v-else
              type="success" 
              class="action-btn view-btn"
              @click="viewTask(task.id)"
            >
              <span class="btn-icon">👁</span>
              <span class="btn-text">查看详情</span>
            </el-button>
          </div>
          
          <!-- 装饰元素 -->
          <div class="task-decoration">
            <div class="corner-line"></div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 任务详情 -->
    <div class="task-detail-container" v-if="currentTask">
      <div class="detail-wrapper">
        <!-- 详情头部 -->
        <div class="detail-header">
          <div class="detail-badge">
            <span class="detail-id">任务 {{ String(currentTask.id).padStart(2, '0') }}</span>
            <el-tag :type="currentTask.statusType" class="detail-status-tag">
              <span class="status-dot"></span>
              <span>{{ currentTask.statusText }}</span>
            </el-tag>
          </div>
          <h2 class="detail-title">{{ currentTask.title }}</h2>
          <div class="detail-environment">
            <span class="env-badge">{{ getEnvironmentName(currentTask.environment) }}</span>
            <span class="difficulty-badge">{{ getDifficulty(currentTask.id) }}</span>
          </div>
        </div>
        
        <!-- 详情内容 -->
        <div class="detail-content">
          <!-- 任务信息卡片 -->
          <div class="info-cards">
            <div class="info-card">
              <div class="info-card-header">
                <span class="info-icon">📋</span>
                <span class="info-title">任务描述</span>
              </div>
              <p class="info-text">{{ currentTask.description }}</p>
            </div>
            
            <div class="info-card">
              <div class="info-card-header">
                <span class="info-icon">🎯</span>
                <span class="info-title">任务目标</span>
              </div>
              <p class="info-text">{{ currentTask.target }}</p>
            </div>
            
            <div class="info-card">
              <div class="info-card-header">
                <span class="info-icon">💡</span>
                <span class="info-title">任务提示</span>
              </div>
              <p class="info-text">{{ currentTask.hint }}</p>
            </div>
          </div>
          
          <!-- 推荐配置 -->
          <div class="config-section">
            <div class="section-header">
              <span class="section-icon">⚙️</span>
              <span class="section-title">推荐配置</span>
            </div>
            <div class="config-list">
              <div 
                v-for="config in currentTask.recommendedConfig" 
                :key="config.param"
                class="config-item"
              >
                <div class="config-left">
                  <span class="config-icon">{{ getConfigIcon(config.param) }}</span>
                  <span class="config-label">{{ config.param }}</span>
                </div>
                <div class="config-right">
                  <span class="config-value">{{ config.value }}</span>
                  <span class="config-arrow">→</span>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <!-- 详情操作 -->
        <div class="detail-actions">
          <el-button 
            v-if="currentTask.inProgress" 
            type="info" 
            @click="completeCurrentTask" 
            class="complete-btn"
          >
            <span class="btn-icon">✓</span>
            <span class="btn-text">标记完成</span>
          </el-button>
          <el-button type="primary" @click="navigateToParamConfig" class="primary-btn">
            <span class="btn-icon">🚀</span>
            <span class="btn-text">前往参数配置</span>
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

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
          task.statusType = savedTask.completed ? 'success' : 'info'
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
    statusType: 'info',
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
    statusType: 'info',
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
    statusType: 'info',
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
    statusType: 'info',
    statusText: '未完成'
  }
])

const currentTask = ref(null)
const activeFilter = ref('all')

// 筛选标签
const filterTabs = [
  { value: 'all', label: '全部任务', icon: '📋' },
  { value: 'in-progress', label: '进行中', icon: '▶' },
  { value: 'completed', label: '已完成', icon: '✅' },
  { value: 'pending', label: '未开始', icon: '⏸' }
]

// 计算属性
const completedCount = computed(() => tasks.value.filter(t => t.completed).length)
const completionRate = computed(() => Math.round((completedCount.value / tasks.value.length) * 100))

// 筛选后的任务
const filteredTasks = computed(() => {
  switch (activeFilter.value) {
    case 'in-progress':
      return tasks.value.filter(t => t.inProgress)
    case 'completed':
      return tasks.value.filter(t => t.completed)
    case 'pending':
      return tasks.value.filter(t => !t.completed && !t.inProgress)
    default:
      return tasks.value
  }
})

// 获取环境图标
const getEnvironmentIcon = (env) => {
  const icons = {
    mountain: '⛰',
    city: '🏙',
    interference: '⚡'
  }
  return icons[env] || '📍'
}

// 获取环境名称
const getEnvironmentName = (env) => {
  const names = {
    mountain: '山地环境',
    city: '城市环境',
    interference: '强干扰'
  }
  return names[env] || '未知环境'
}

// 获取难度
const getDifficulty = (id) => {
  const difficulties = ['入门', '进阶', '高级', '精英']
  return difficulties[(id - 1) % difficulties.length]
}

// 获取配置图标
const getConfigIcon = (param) => {
  const icons = {
    '频率': '📡',
    '加密方式': '🔐',
    '功率': '⚡',
    '抗干扰模式': '🛡'
  }
  return icons[param] || '⚙️'
}

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
      task.statusType = 'info'
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
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 30px;
}

.page-header.compact {
  margin-bottom: 30px;
  justify-content: flex-start;
}

.header-content {
  text-align: left;
  flex: 1;
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

/* 统计卡片 */
.stats-cards {
  display: flex;
  gap: 16px;
  flex-wrap: wrap;
}

.stat-card {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 24px;
  background: linear-gradient(135deg, rgba(31, 41, 55, 0.5) 0%, rgba(31, 41, 55, 0.3) 100%);
  border: 1px solid #1f2937;
  border-radius: 12px;
  position: relative;
  overflow: hidden;
}

.stat-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 3px;
  height: 100%;
  background: linear-gradient(180deg, #00ff88 0%, #00d4ff 100%);
  opacity: 0.5;
}

.stat-icon-wrapper {
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.stat-icon-wrapper.accent {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.15) 0%, rgba(0, 255, 136, 0.15) 100%);
}

.stat-icon-wrapper.success {
  background: linear-gradient(135deg, rgba(102, 194, 58, 0.15) 0%, rgba(0, 255, 136, 0.15) 100%);
}

.stat-icon {
  font-size: 20px;
}

.stat-content {
  display: flex;
  flex-direction: column;
}

.stat-value {
  font-size: 28px;
  font-weight: 800;
  font-family: 'Courier New', monospace;
  color: #00ff88;
  line-height: 1;
  margin-bottom: 4px;
}

.stat-label {
  font-size: 12px;
  color: #6b7280;
  letter-spacing: 1px;
}

/* 返回按钮 */
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
  transition: all 0.3s ease;
}

.back-button:hover {
  background: rgba(31, 41, 55, 0.8);
  border-color: #374151;
}

.back-icon {
  font-size: 18px;
}

/* 筛选标签 */
.filter-tabs {
  display: flex;
  gap: 12px;
  margin-bottom: 32px;
  flex-wrap: wrap;
}

.filter-tab {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: rgba(31, 41, 55, 0.4);
  border: 1px solid #1f2937;
  border-radius: 50px;
  color: #6b7280;
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.filter-tab:hover {
  background: rgba(31, 41, 55, 0.7);
  border-color: #374151;
  color: #e0e6ed;
}

.filter-tab.active {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.2) 0%, rgba(0, 212, 255, 0.15) 100%);
  border: 1px solid rgba(0, 255, 136, 0.3);
  color: #00ff88;
}

.tab-icon {
  font-size: 14px;
}

/* 任务网格 */
.task-list-container {
  max-width: 1400px;
}

.task-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 24px;
}

/* 任务项 */
.task-item {
  background: #0a0e17;
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
  position: relative;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
}

.task-item:hover {
  transform: translateY(-6px);
  border-color: #374151;
  box-shadow: 0 16px 48px rgba(0, 0, 0, 0.4);
}

.task-item.task-completed {
  opacity: 0.7;
}

.task-item.task-in-progress {
  border-color: rgba(255, 165, 0, 0.4);
}

/* 任务装饰 */
.task-decoration {
  position: absolute;
  top: 0;
  right: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.corner-line {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 60px;
  border-top: 2px solid rgba(0, 255, 136, 0.3);
  border-right: 2px solid rgba(0, 255, 136, 0.3);
  border-radius: 0 16px 0 0;
}

.task-item.task-in-progress .corner-line {
  border-color: rgba(255, 165, 0, 0.5);
}

.task-item.task-completed .corner-line {
  border-color: rgba(102, 194, 58, 0.5);
}

/* 任务头部 */
.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.id-badge {
  width: 44px;
  height: 44px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
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

.status-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: currentColor;
}

.task-status.is-warning .status-dot {
  animation: blink 1.5s ease-in-out infinite;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.3; }
}

/* 任务内容 */
.task-content {
  margin-bottom: 20px;
}

.task-title-row {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}

.task-environment-icon {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.env-mountain {
  background: linear-gradient(135deg, rgba(102, 194, 58, 0.2) 0%, rgba(0, 255, 136, 0.1) 100%);
}

.env-city {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.2) 0%, rgba(0, 255, 136, 0.1) 100%);
}

.env-interference {
  background: linear-gradient(135deg, rgba(255, 107, 107, 0.2) 0%, rgba(255, 165, 0, 0.1) 100%);
}

.env-icon {
  font-size: 20px;
}

.task-title {
  font-size: 18px;
  font-weight: 700;
  margin: 0;
  line-height: 1.3;
}

.task-description {
  font-size: 13px;
  color: #9ca3af;
  margin: 0 0 16px 0;
  line-height: 1.6;
}

.task-target {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 14px;
  background: rgba(0, 255, 136, 0.05);
  border: 1px solid rgba(0, 255, 136, 0.1);
  border-radius: 8px;
  margin-bottom: 16px;
}

.target-icon {
  font-size: 14px;
}

.target-text {
  font-size: 13px;
  color: #00ff88;
  font-weight: 500;
}

.task-tags {
  display: flex;
  gap: 8px;
}

.env-tag {
  background: rgba(31, 41, 55, 0.6);
  border: 1px solid #1f2937;
  color: #e0e6ed;
}

.difficulty-tag {
  background: rgba(255, 165, 0, 0.1);
  border: 1px solid rgba(255, 165, 0, 0.2);
  color: #ffa500;
}

/* 任务底部 */
.task-footer {
  padding-top: 16px;
  border-top: 1px solid #1f2937;
}

.action-btn {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 12px 20px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.3s ease;
}

.start-btn {
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  color: #0a0e14;
  border: none;
}

.start-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0, 255, 136, 0.3);
}

.continue-btn {
  background: linear-gradient(135deg, #ffa500 0%, #cc8400 100%);
  color: #0a0e14;
  border: none;
}

.continue-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(255, 165, 0, 0.3);
}

.view-btn {
  background: rgba(31, 41, 55, 0.6);
  color: #e0e6ed;
  border: 1px solid #1f2937;
}

.view-btn:hover {
  background: rgba(31, 41, 55, 0.9);
  border-color: #374151;
}

/* 任务详情 */
.task-detail-container {
  max-width: 900px;
}

.detail-wrapper {
  background: #0a0e17;
  border: 1px solid #1f2937;
  border-radius: 20px;
  padding: 40px;
  position: relative;
  overflow: hidden;
}

.detail-wrapper::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent 0%, #00ff88 20%, #00d4ff 50%, #00ff88 80%, transparent 100%);
}

.detail-header {
  margin-bottom: 40px;
  text-align: center;
}

.detail-badge {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 20px;
}

.detail-id {
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
}

.detail-status-tag {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 20px;
  border-radius: 50px;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 1px;
}

.detail-title {
  font-size: 32px;
  font-weight: 800;
  margin: 0 0 20px 0;
  letter-spacing: 1px;
}

.detail-environment {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
}

.env-badge,
.difficulty-badge {
  padding: 6px 16px;
  border-radius: 50px;
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 1px;
}

.env-badge {
  background: rgba(31, 41, 55, 0.6);
  border: 1px solid #1f2937;
  color: #e0e6ed;
}

.difficulty-badge {
  background: rgba(255, 165, 0, 0.1);
  border: 1px solid rgba(255, 165, 0, 0.2);
  color: #ffa500;
}

/* 详情内容 */
.detail-content {
  margin-bottom: 40px;
}

/* 信息卡片 */
.info-cards {
  display: grid;
  gap: 16px;
  margin-bottom: 32px;
}

.info-card {
  background: linear-gradient(135deg, rgba(31, 41, 55, 0.4) 0%, rgba(31, 41, 55, 0.25) 100%);
  border: 1px solid #1f2937;
  border-radius: 12px;
  padding: 20px;
}

.info-card-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}

.info-icon {
  font-size: 18px;
}

.info-title {
  font-size: 14px;
  font-weight: 700;
  color: #9ca3af;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.info-text {
  font-size: 14px;
  color: #d1d5db;
  line-height: 1.7;
  margin: 0;
  padding-left: 28px;
}

/* 配置部分 */
.config-section {
  background: linear-gradient(135deg, rgba(31, 41, 55, 0.4) 0%, rgba(31, 41, 55, 0.25) 100%);
  border: 1px solid #1f2937;
  border-radius: 12px;
  padding: 24px;
}

.section-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
}

.section-icon {
  font-size: 18px;
}

.section-title {
  font-size: 14px;
  font-weight: 700;
  color: #9ca3af;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.config-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.config-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  background: rgba(31, 41, 55, 0.5);
  border: 1px solid #1f2937;
  border-radius: 10px;
  transition: all 0.3s ease;
}

.config-item:hover {
  background: rgba(31, 41, 55, 0.7);
  border-color: #374151;
}

.config-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.config-icon {
  font-size: 18px;
}

.config-label {
  font-size: 14px;
  color: #6b7280;
  font-weight: 500;
}

.config-right {
  display: flex;
  align-items: center;
  gap: 10px;
}

.config-value {
  font-size: 15px;
  color: #00ff88;
  font-weight: 600;
  font-family: 'Courier New', monospace;
}

.config-arrow {
  font-size: 14px;
  color: #6b7280;
  transition: transform 0.3s ease;
}

.config-item:hover .config-arrow {
  transform: translateX(4px);
}

/* 详情操作 */
.detail-actions {
  display: flex;
  gap: 16px;
  padding-top: 32px;
  border-top: 1px solid #1f2937;
}

.primary-btn {
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
  transition: all 0.3s ease;
}

.primary-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(0, 255, 136, 0.35);
}

.complete-btn {
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
  transition: all 0.3s ease;
}

.complete-btn:hover {
  background: rgba(0, 255, 136, 0.2);
  border-color: rgba(0, 255, 136, 0.5);
}

.btn-icon {
  font-size: 18px;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    gap: 20px;
  }
  
  .stats-cards {
    width: 100%;
  }
  
  .stat-card {
    flex: 1;
    min-width: 120px;
  }
  
  .task-grid {
    grid-template-columns: 1fr;
  }
  
  .detail-wrapper {
    padding: 24px;
  }
  
  .detail-actions {
    flex-direction: column;
  }
  
  .complete-btn,
  .primary-btn {
    width: 100%;
  }
}
</style>
