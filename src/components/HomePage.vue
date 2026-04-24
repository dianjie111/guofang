<template>
  <div class="home-page">
    <!-- 顶部英雄区域 -->
    <div class="hero-section">
      <div class="hero-content">
        <div class="hero-badge">
          <span class="badge-icon">◆</span>
          <span class="badge-text">战术指挥中心</span>
        </div>
        <h1 class="hero-title">
          <span class="title-line">野战通信</span>
          <span class="title-line accent">参数配置训练</span>
        </h1>
        <p class="hero-subtitle">
          科技守卫家园 · 军事通信模拟训练系统
        </p>
      </div>
      <div class="hero-decoration">
        <div class="grid-pattern"></div>
        <div class="glow-circle"></div>
      </div>
    </div>
    
    <div class="home-content">
      <!-- 快速开始训练 -->
      <div class="section-wrapper">
        <div class="section-header">
          <div class="section-title-block">
            <span class="section-number">01</span>
            <h2 class="section-title">快速部署</h2>
          </div>
          <div class="section-line"></div>
        </div>
        <div class="quick-start-card">
          <p class="quick-desc">选择战场环境，立即体验通信参数配置</p>
          <div class="environment-cards">
            <div 
              v-for="env in environments" 
              :key="env.value"
              class="env-card"
              :class="{ active: selectedEnv === env.value }"
              @click="selectEnv(env.value)"
            >
              <div class="env-background" :style="{ background: env.gradient }"></div>
              <div class="env-content">
                <div class="env-icon">{{ env.icon }}</div>
                <div class="env-name">{{ env.name }}</div>
                <div class="env-tag">{{ env.tag }}</div>
              </div>
            </div>
          </div>
          <button class="start-btn" @click="startTraining">
            <span class="btn-text">开始训练</span>
            <span class="btn-arrow">→</span>
          </button>
        </div>
      </div>
      
      <!-- 平台统计 -->
      <div class="section-wrapper">
        <div class="section-header">
          <div class="section-title-block">
            <span class="section-number">02</span>
            <h2 class="section-title">作战数据</h2>
          </div>
          <div class="section-line"></div>
        </div>
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-header">
              <div class="stat-icon-wrapper">
                <span class="stat-icon">🎯</span>
              </div>
              <span class="stat-label">训练任务</span>
            </div>
            <div class="stat-value">{{ taskStats.total }}</div>
            <div class="stat-detail">
              <span class="detail-item pending">待完成: {{ taskStats.pending }}</span>
              <span class="detail-divider">/</span>
              <span class="detail-item completed">已完成: {{ taskStats.completed }}</span>
            </div>
          </div>
          
          <div class="stat-card">
            <div class="stat-header">
              <div class="stat-icon-wrapper accent">
                <span class="stat-icon">🏆</span>
              </div>
              <span class="stat-label">最佳场景</span>
            </div>
            <div class="stat-value accent">{{ bestScores.best || '--' }}<span class="value-unit">分</span></div>
            <div class="stat-detail scores">
              <span>山地: {{ bestScores.mountain || '--' }}</span>
              <span>城市: {{ bestScores.city || '--' }}</span>
              <span>干扰: {{ bestScores.interference || '--' }}</span>
            </div>
          </div>
          
          <div class="stat-card">
            <div class="stat-header">
              <div class="stat-icon-wrapper success">
                <span class="stat-icon">📈</span>
              </div>
              <span class="stat-label">综合通过率</span>
            </div>
            <div class="stat-value success">{{ passRate }}<span class="value-unit">%</span></div>
            <div class="stat-progress">
              <div class="progress-bar">
                <div class="progress-fill" :style="{ width: passRate + '%', background: passRateColor }"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <div class="two-column-layout">
        <!-- 推荐训练 -->
        <div class="column section-wrapper">
          <div class="section-header compact">
            <div class="section-title-block">
              <span class="section-number small">03</span>
              <h2 class="section-title small">推荐任务</h2>
            </div>
          </div>
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
                <span class="action-icon">▶</span>
              </div>
            </div>
          </div>
        </div>
        
        <!-- 今日推荐 -->
        <div class="column section-wrapper">
          <div class="section-header compact">
            <div class="section-title-block">
              <span class="section-number small">04</span>
              <h2 class="section-title small">战术知识库</h2>
            </div>
          </div>
          <div class="knowledge-list">
            <div 
              v-for="item in knowledgeArticles" 
              :key="item.id"
              class="knowledge-item"
              @click="goToKnowledge(item.tab, item.id)"
            >
              <div class="knowledge-icon">
                <span>💡</span>
              </div>
              <div class="knowledge-content">
                <div class="knowledge-title">{{ item.title }}</div>
              </div>
              <div class="knowledge-action">
                <span class="action-arrow">→</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 历史记录 -->
      <div class="section-wrapper history-section">
        <div class="section-header">
          <div class="section-title-block">
            <span class="section-number">05</span>
            <h2 class="section-title">作战日志</h2>
          </div>
          <div class="section-line"></div>
        </div>
        <div class="history-wrapper">
          <HistoryRecord ref="historyRecordRef" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import HistoryRecord from './HistoryRecord.vue'

const historyRecordRef = ref(null)

onMounted(() => {
  window.historyRecordRef = historyRecordRef.value
})

onUnmounted(() => {
  window.historyRecordRef = null
})

const selectedEnv = ref('mountain')

const environments = [
  { 
    value: 'mountain', 
    name: '山地环境', 
    icon: '🏔️', 
    gradient: 'linear-gradient(135deg, #00ff88 0%, #00cc6a 50%, #009950 100%)',
    tag: '远距离'
  },
  { 
    value: 'city', 
    name: '城市环境', 
    icon: '🏙️', 
    gradient: 'linear-gradient(135deg, #00d4ff 0%, #0099cc 50%, #006699 100%)',
    tag: '高穿透'
  },
  { 
    value: 'interference', 
    name: '强干扰环境', 
    icon: '⚡', 
    gradient: 'linear-gradient(135deg, #ff6b6b 0%, #cc5555 50%, #993333 100%)',
    tag: '抗干扰'
  }
]

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

const updateTaskStats = (completedCount) => {
  taskStats.value.completed = completedCount
  taskStats.value.pending = taskStats.value.total - completedCount
  saveStatsToStorage()
}

const updateBestScore = (environment, score) => {
  if (score > bestScores.value[environment]) {
    bestScores.value[environment] = score
    bestScores.value.best = Math.max(...Object.values(bestScores.value).filter(v => typeof v === 'number'))
    saveStatsToStorage()
  }
}

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

onMounted(() => {
  loadStatsFromStorage()
})

const passRate = computed(() => {
  return Math.round((taskStats.value.completed / taskStats.value.total) * 100)
})

const passRateColor = computed(() => {
  if (passRate.value >= 80) return '#00ff88'
  if (passRate.value >= 60) return '#ffa500'
  return '#ff6b6b'
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
  if (window.switchToParamConfig) {
    window.switchToParamConfig(selectedEnv.value)
  }
}

const goToTask = (taskId) => {
  if (window.goToTask) {
    window.goToTask(taskId)
  }
}

const goToKnowledge = (tab, articleId) => {
  if (window.goToKnowledge) {
    window.goToKnowledge(tab, articleId)
  }
}
</script>

<style scoped>
.home-page {
  padding: 0;
}

/* 英雄区域 */
.hero-section {
  position: relative;
  padding: 40px 0 50px;
  margin-bottom: 40px;
  overflow: hidden;
}

.hero-content {
  position: relative;
  z-index: 2;
}

.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba(0, 255, 136, 0.1);
  border: 1px solid rgba(0, 255, 136, 0.2);
  border-radius: 50px;
  margin-bottom: 24px;
}

.badge-icon {
  color: #00ff88;
  font-size: 14px;
}

.badge-text {
  font-size: 12px;
  color: #00ff88;
  letter-spacing: 2px;
  font-weight: 600;
}

.hero-title {
  margin: 0 0 12px 0;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.title-line {
  font-size: 42px;
  font-weight: 800;
  letter-spacing: 4px;
  line-height: 1.1;
}

.title-line.accent {
  background: linear-gradient(90deg, #00ff88 0%, #00d4ff 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero-subtitle {
  font-size: 16px;
  color: #6b7280;
  margin: 0;
  letter-spacing: 1px;
}

.hero-decoration {
  position: absolute;
  top: 0;
  right: 0;
  width: 50%;
  height: 100%;
  pointer-events: none;
}

.grid-pattern {
  position: absolute;
  top: 0;
  right: 0;
  width: 400px;
  height: 400px;
  background-image: 
    linear-gradient(rgba(0, 255, 136, 0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0, 255, 136, 0.03) 1px, transparent 1px);
  background-size: 40px 40px;
  transform: rotate(45deg);
}

.glow-circle {
  position: absolute;
  top: 50%;
  right: 10%;
  width: 300px;
  height: 300px;
  background: radial-gradient(circle, rgba(0, 255, 136, 0.1) 0%, transparent 70%);
  transform: translateY(-50%);
}

/* 通用布局 */
.home-content {
  max-width: 1400px;
  margin: 0 auto;
}

.section-wrapper {
  margin-bottom: 40px;
}

.section-header {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 24px;
}

.section-header.compact {
  margin-bottom: 16px;
}

.section-title-block {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-shrink: 0;
}

.section-number {
  font-size: 14px;
  font-weight: 800;
  color: #00ff88;
  font-family: 'Courier New', monospace;
}

.section-number.small {
  font-size: 12px;
}

.section-title {
  font-size: 20px;
  font-weight: 700;
  margin: 0;
  letter-spacing: 2px;
}

.section-title.small {
  font-size: 16px;
}

.section-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(90deg, #1f2937 0%, transparent 100%);
}

/* 快速开始 */
.quick-start-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.9) 0%, rgba(10, 14, 20, 0.95) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 32px;
  position: relative;
  overflow: hidden;
}

.quick-start-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent 0%, #00ff88 20%, #00d4ff 50%, #00ff88 80%, transparent 100%);
}

.quick-desc {
  font-size: 14px;
  color: #9ca3af;
  margin-bottom: 28px;
  text-align: center;
}

.environment-cards {
  display: flex;
  justify-content: center;
  gap: 24px;
  margin-bottom: 32px;
}

.env-card {
  position: relative;
  cursor: pointer;
  padding: 28px 24px;
  border-radius: 16px;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  text-align: center;
  width: 160px;
  background: rgba(31, 41, 55, 0.5);
  border: 2px solid #1f2937;
  overflow: hidden;
}

.env-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  transition: opacity 0.4s ease;
}

.env-card:hover .env-background {
  opacity: 0.1;
}

.env-card.active {
  border-color: #00ff88;
  transform: translateY(-4px);
  box-shadow: 0 8px 30px rgba(0, 255, 136, 0.2);
}

.env-card.active .env-background {
  opacity: 0.15;
}

.env-content {
  position: relative;
  z-index: 2;
}

.env-icon {
  font-size: 40px;
  margin-bottom: 12px;
}

.env-name {
  font-size: 15px;
  font-weight: 600;
  margin-bottom: 8px;
}

.env-tag {
  display: inline-block;
  padding: 4px 12px;
  background: rgba(0, 255, 136, 0.1);
  border-radius: 50px;
  font-size: 11px;
  color: #00ff88;
  letter-spacing: 1px;
}

.start-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  width: 240px;
  height: 56px;
  margin: 0 auto;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 700;
  color: #0a0e14;
  cursor: pointer;
  transition: all 0.3s ease;
  letter-spacing: 2px;
}

.start-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 30px rgba(0, 255, 136, 0.4);
}

.btn-arrow {
  font-size: 20px;
  transition: transform 0.3s ease;
}

.start-btn:hover .btn-arrow {
  transform: translateX(4px);
}

/* 统计卡片 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
}

.stat-card {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.9) 0%, rgba(10, 14, 20, 0.95) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 28px;
  position: relative;
  overflow: hidden;
}

.stat-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: #00ff88;
  opacity: 0.5;
}

.stat-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.stat-icon-wrapper {
  width: 44px;
  height: 44px;
  background: rgba(0, 255, 136, 0.1);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.stat-icon-wrapper.accent {
  background: rgba(0, 212, 255, 0.1);
}

.stat-icon-wrapper.success {
  background: rgba(102, 194, 58, 0.1);
}

.stat-icon {
  font-size: 24px;
}

.stat-label {
  font-size: 13px;
  color: #6b7280;
  letter-spacing: 1px;
}

.stat-value {
  font-size: 40px;
  font-weight: 800;
  line-height: 1;
  margin-bottom: 16px;
  font-family: 'Courier New', monospace;
}

.stat-value.accent {
  color: #00d4ff;
}

.stat-value.success {
  color: #00ff88;
}

.value-unit {
  font-size: 20px;
  margin-left: 4px;
}

.stat-detail {
  font-size: 12px;
  color: #6b7280;
}

.detail-item.pending {
  color: #ffa500;
}

.detail-item.completed {
  color: #00ff88;
}

.detail-divider {
  margin: 0 6px;
  color: #374151;
}

.stat-detail.scores {
  display: flex;
  gap: 16px;
}

.stat-progress {
  margin-top: 8px;
}

.progress-bar {
  height: 6px;
  background: #1f2937;
  border-radius: 10px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  border-radius: 10px;
  transition: width 0.5s ease;
}

/* 两列布局 */
.two-column-layout {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-bottom: 40px;
}

.column {
  margin-bottom: 0;
}

/* 任务列表 */
.task-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.task-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px;
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.9) 0%, rgba(10, 14, 20, 0.95) 100%);
  border: 1px solid #1f2937;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.task-item:hover {
  border-color: #00ff88;
  transform: translateX(4px);
}

.task-level {
  padding: 6px 14px;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 1px;
  flex-shrink: 0;
}

.level-beginner {
  background: rgba(102, 194, 58, 0.15);
  color: #00ff88;
}

.level-intermediate {
  background: rgba(230, 162, 60, 0.15);
  color: #ffa500;
}

.level-advanced {
  background: rgba(0, 212, 255, 0.15);
  color: #00d4ff;
}

.task-info {
  flex: 1;
  min-width: 0;
}

.task-title {
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 4px;
}

.task-desc {
  font-size: 12px;
  color: #6b7280;
}

.task-action {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 255, 136, 0.1);
  border-radius: 8px;
  flex-shrink: 0;
}

.action-icon {
  font-size: 12px;
  color: #00ff88;
}

/* 知识列表 */
.knowledge-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.knowledge-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px;
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.9) 0%, rgba(10, 14, 20, 0.95) 100%);
  border: 1px solid #1f2937;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.knowledge-item:hover {
  border-color: #00d4ff;
  transform: translateX(4px);
}

.knowledge-icon {
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 212, 255, 0.1);
  border-radius: 10px;
  flex-shrink: 0;
}

.knowledge-icon span {
  font-size: 22px;
}

.knowledge-content {
  flex: 1;
  min-width: 0;
}

.knowledge-title {
  font-size: 14px;
  font-weight: 500;
}

.knowledge-action {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.action-arrow {
  font-size: 20px;
  color: #00d4ff;
  transition: transform 0.3s ease;
}

.knowledge-item:hover .action-arrow {
  transform: translateX(4px);
}

/* 历史记录 */
.history-section {
  margin-bottom: 0;
}

.history-wrapper {
  background: linear-gradient(135deg, rgba(13, 17, 23, 0.9) 0%, rgba(10, 14, 20, 0.95) 100%);
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
}
</style>