<template>
  <div class="home-page">
    <div class="home-header">
      <h1>📻 野战通信参数配置训练模拟器</h1>
      <p class="subtitle">科技守卫家园 · 军事通信模拟训练系统</p>
    </div>
    
    <div class="home-content">
      <!-- 快速开始训练 -->
      <el-card class="quick-start-card">
        <template #header>
          <span>⚡ 快速开始训练</span>
        </template>
        <div class="quick-start-content">
          <p class="quick-desc">选择战场环境，立即体验通信参数配置</p>
          <div class="environment-cards">
            <div 
              v-for="env in environments" 
              :key="env.value"
              class="env-card"
              :class="{ active: selectedEnv === env.value }"
              @click="selectEnv(env.value)"
            >
              <div class="env-icon" :style="{ backgroundColor: env.color }">
                {{ env.icon }}
              </div>
              <div class="env-name">{{ env.name }}</div>
            </div>
          </div>
          <el-button type="primary" size="large" @click="startTraining" class="start-btn">
            开始训练
          </el-button>
        </div>
      </el-card>
      
      <!-- 平台统计 -->
      <el-card class="stats-card">
        <template #header>
          <span>📊 平台统计</span>
        </template>
        <el-row :gutter="20">
          <el-col :span="8">
            <div class="stat-item">
              <div class="stat-icon">🎯</div>
              <div class="stat-info">
                <div class="stat-value">{{ taskStats.total }}</div>
                <div class="stat-label">训练任务</div>
                <div class="stat-detail">待完成: {{ taskStats.pending }}个 / 已完成: {{ taskStats.completed }}个</div>
              </div>
            </div>
          </el-col>
          <el-col :span="8">
            <div class="stat-item">
              <div class="stat-icon">🏆</div>
              <div class="stat-info">
                <div class="stat-value">{{ bestScores.best || '--' }}分</div>
                <div class="stat-label">最佳场景</div>
                <div class="stat-detail">山地: {{ bestScores.mountain || '--' }}分 / 城市: {{ bestScores.city || '--' }}分 / 干扰: {{ bestScores.interference || '--' }}分</div>
              </div>
            </div>
          </el-col>
          <el-col :span="8">
            <div class="stat-item">
              <div class="stat-icon">📈</div>
              <div class="stat-info">
                <div class="stat-value">{{ passRate }}%</div>
                <div class="stat-label">综合通过率</div>
                <div class="stat-detail">
                  <el-progress :percentage="passRate" :color="passRateColor" :show-text="false"></el-progress>
                </div>
              </div>
            </div>
          </el-col>
        </el-row>
      </el-card>
      
      <!-- 推荐训练 -->
      <el-card class="recommend-task-card">
        <template #header>
          <span>🎯 推荐训练</span>
        </template>
        <div class="task-list">
          <div 
            v-for="(task, index) in recommendTasks" 
            :key="task.id"
            class="task-item"
            @click="goToTask(task.id)"
          >
            <div class="task-level" :class="'level-' + task.level">
              {{ task.levelText }}
            </div>
            <div class="task-info">
              <div class="task-title">{{ task.title }}</div>
              <div class="task-desc">{{ task.description }}</div>
            </div>
            <div class="task-action">
              <span>开始挑战 →</span>
            </div>
          </div>
        </div>
      </el-card>
      
      <!-- 今日推荐 -->
      <el-card class="today-recommend-card">
        <template #header>
          <span>📚 今日推荐</span>
        </template>
        <div class="knowledge-list">
          <div 
            v-for="item in knowledgeArticles" 
            :key="item.id"
            class="knowledge-item"
            @click="goToKnowledge(item.tab, item.id)"
          >
            <div class="knowledge-icon">💡</div>
            <div class="knowledge-title">{{ item.title }}</div>
            <div class="knowledge-action">阅读全文 →</div>
          </div>
        </div>
      </el-card>
      
      <!-- 历史记录 -->
      <div class="history-record-section">
        <h3>📋 历史记录</h3>
        <HistoryRecord ref="historyRecordRef" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import HistoryRecord from './HistoryRecord.vue'

const historyRecordRef = ref(null)

onMounted(() => {
  // 暴露给全局
  window.historyRecordRef = historyRecordRef.value
})

onUnmounted(() => {
  window.historyRecordRef = null
})

const selectedEnv = ref('mountain')

const environments = [
  { value: 'mountain', name: '山地环境', icon: '🏔️', color: '#4CAF50' },
  { value: 'city', name: '城市环境', icon: '🏙️', color: '#9E9E9E' },
  { value: 'interference', name: '强干扰环境', icon: '⚡', color: '#F44336' }
]

// 从localStorage读取平台统计数据
const loadStatsFromStorage = () => {
  try {
    const storedStats = localStorage.getItem('trainingStats')
    if (storedStats) {
      const parsed = JSON.parse(storedStats)
      taskStats.value = parsed.taskStats || { total: 4, pending: 4, completed: 0 }
      bestScores.value = parsed.bestScores || { best: 0, mountain: 0, city: 0, interference: 0 }
    }
  } catch (error) {
    console.error('Failed to load stats from storage:', error)
  }
}

// 保存平台统计数据到localStorage
const saveStatsToStorage = () => {
  try {
    const statsToSave = {
      taskStats: taskStats.value,
      bestScores: bestScores.value
    }
    localStorage.setItem('trainingStats', JSON.stringify(statsToSave))
  } catch (error) {
    console.error('Failed to save stats to storage:', error)
  }
}

// 更新任务统计数据
const updateTaskStats = (completedCount) => {
  taskStats.value.completed = completedCount
  taskStats.value.pending = taskStats.value.total - completedCount
  saveStatsToStorage()
}

// 更新最佳成绩
const updateBestScore = (environment, score) => {
  if (score > bestScores.value[environment]) {
    bestScores.value[environment] = score
    bestScores.value.best = Math.max(...Object.values(bestScores.value).filter(v => typeof v === 'number'))
    saveStatsToStorage()
  }
}

// 暴露更新函数到全局
window.updateTaskStats = updateTaskStats
window.updateBestScore = updateBestScore

const taskStats = ref({
  total: 4,
  pending: 4,
  completed: 0
})

const bestScores = ref({
  best: 0,
  mountain: 0,
  city: 0,
  interference: 0
})

// 组件初始化时加载统计数据
onMounted(() => {
  loadStatsFromStorage()
})

const passRate = computed(() => {
  return Math.round((taskStats.value.completed / taskStats.value.total) * 100)
})

const passRateColor = computed(() => {
  if (passRate.value >= 80) return '#67C23A'
  if (passRate.value >= 60) return '#E6A23C'
  return '#F56C6C'
})

const recommendTasks = ref([
  { 
    id: 1, 
    title: '山地通信基础', 
    level: 'beginner', 
    levelText: '新手入门',
    description: '学习山地环境下的HF频段通信技巧'
  },
  { 
    id: 2, 
    title: '抗干扰通信演练', 
    level: 'intermediate', 
    levelText: '进阶训练',
    description: '掌握跳频和自适应调频技术'
  },
  { 
    id: 4, 
    title: '城市环境精通', 
    level: 'advanced', 
    levelText: '高手挑战',
    description: '熟练运用UHF频段和扩频技术'
  }
])

const knowledgeArticles = ref([
  { id: 1, title: 'HF频段为什么适合山地通信？', tab: 'frequency' },
  { id: 2, title: '跳频技术是如何抗干扰的？', tab: 'anti-interference' }
])

const selectEnv = (env) => {
  selectedEnv.value = env
}

const startTraining = () => {
  // 跳转到参数配置页面，并传递选中的环境
  if (window.switchToParamConfig) {
    window.switchToParamConfig(selectedEnv.value)
  }
}

const goToTask = (taskId) => {
  // 跳转到训练任务页面并选中对应任务
  if (window.goToTask) {
    window.goToTask(taskId)
  }
}

const goToKnowledge = (tab, articleId) => {
  // 跳转到知识库对应文章
  if (window.goToKnowledge) {
    window.goToKnowledge(tab, articleId)
  }
}
</script>

<style scoped>
.home-page {
  padding: 20px;
}

.home-header {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px 0;
}

.home-header h1 {
  font-size: 28px;
  font-weight: bold;
  color: #303133;
  margin: 0 0 10px 0;
}

.subtitle {
  font-size: 16px;
  color: #909399;
  margin: 0;
}

.home-content {
  max-width: 1200px;
  margin: 0 auto;
}

.home-content .el-card {
  margin-bottom: 20px;
}

/* 快速开始训练 */
.quick-start-content {
  text-align: center;
}

.quick-desc {
  font-size: 14px;
  color: #606266;
  margin-bottom: 20px;
}

.environment-cards {
  display: flex;
  justify-content: center;
  gap: 30px;
  margin-bottom: 30px;
}

.env-card {
  cursor: pointer;
  padding: 20px;
  border: 2px solid #e4e7ed;
  border-radius: 12px;
  transition: all 0.3s ease;
  text-align: center;
  width: 120px;
}

.env-card:hover {
  border-color: #409EFF;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.env-card.active {
  border-color: #409EFF;
  background-color: #ecf5ff;
}

.env-icon {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 10px;
  font-size: 28px;
}

.env-name {
  font-size: 14px;
  font-weight: 500;
  color: #303133;
}

.start-btn {
  width: 200px;
  height: 50px;
  font-size: 16px;
}

/* 平台统计 */
.stat-item {
  display: flex;
  align-items: flex-start;
  gap: 15px;
  padding: 15px;
  background-color: #f5f7fa;
  border-radius: 8px;
}

.stat-icon {
  font-size: 32px;
}

.stat-info {
  flex: 1;
}

.stat-value {
  font-size: 28px;
  font-weight: bold;
  color: #1890ff;
  line-height: 1;
}

.stat-label {
  font-size: 14px;
  color: #606266;
  margin-top: 5px;
}

.stat-detail {
  font-size: 12px;
  color: #909399;
  margin-top: 5px;
}

/* 推荐训练 */
.task-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.task-item {
  display: flex;
  align-items: center;
  padding: 15px;
  border: 1px solid #e4e7ed;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.task-item:hover {
  border-color: #409EFF;
  background-color: #ecf5ff;
}

.task-level {
  padding: 5px 12px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 500;
  margin-right: 15px;
}

.level-beginner {
  background-color: #f0f9eb;
  color: #67C23A;
}

.level-intermediate {
  background-color: #fdf6ec;
  color: #E6A23C;
}

.level-advanced {
  background-color: #ecf5ff;
  color: #409EFF;
}

.task-info {
  flex: 1;
}

.task-title {
  font-size: 14px;
  font-weight: 500;
  color: #303133;
}

.task-desc {
  font-size: 12px;
  color: #909399;
  margin-top: 4px;
}

.task-action {
  color: #409EFF;
  font-size: 14px;
}

/* 今日推荐 */
.knowledge-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.knowledge-item {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  border: 1px solid #e4e7ed;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.knowledge-item:hover {
  border-color: #409EFF;
  background-color: #ecf5ff;
}

.knowledge-icon {
  font-size: 20px;
  margin-right: 12px;
}

.knowledge-title {
  flex: 1;
  font-size: 14px;
  color: #303133;
}

.knowledge-action {
  color: #409EFF;
  font-size: 14px;
}

.history-record-section {
  margin-top: 30px;
}

.history-record-section h3 {
  font-size: 18px;
  font-weight: bold;
  color: #303133;
  margin-bottom: 15px;
}
</style>