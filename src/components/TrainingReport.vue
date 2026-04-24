<template>
  <el-card class="training-report-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>训练报告</span>
      </div>
    </template>
    <div class="report-content">
      <el-row :gutter="20">
        <el-col :span="12">
          <el-card class="report-card">
            <template #header>
              <span>历史成绩</span>
            </template>
            <el-table :data="historyScores" style="width: 100%">
              <el-table-column prop="date" label="日期" width="180"></el-table-column>
              <el-table-column prop="task" label="任务" width="180"></el-table-column>
              <el-table-column prop="score" label="分数" width="100">
                <template #default="scope">
                  <el-progress :percentage="scope.row.score" :color="getScoreColor(scope.row.score)" :stroke-width="10"></el-progress>
                </template>
              </el-table-column>
              <el-table-column prop="level" label="等级"></el-table-column>
            </el-table>
          </el-card>
        </el-col>
        <el-col :span="12">
          <el-card class="report-card">
            <template #header>
              <span>进步曲线</span>
            </template>
            <div class="progress-chart">
              <div class="chart-container">
                <div class="chart-axis">
                  <div class="x-axis">
                    <span v-for="(item, index) in historyScores" :key="index">{{ item.date }}</span>
                  </div>
                  <div class="y-axis">
                    <span>100</span>
                    <span>80</span>
                    <span>60</span>
                    <span>40</span>
                    <span>20</span>
                    <span>0</span>
                  </div>
                </div>
                <div class="chart-data">
                  <div class="data-line">
                    <div 
                      v-for="(item, index) in historyScores" 
                      :key="index"
                      class="data-point"
                      :style="{
                        left: `${index * (100 / (historyScores.length - 1))}%`,
                        bottom: `${item.score}%`
                      }"
                    >
                      <div class="data-label">{{ item.score }}</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </el-card>
        </el-col>
      </el-row>
      <el-row :gutter="20" style="margin-top: 20px;">
        <el-col :span="24">
          <el-card class="report-card">
            <template #header>
              <span>训练统计</span>
            </template>
            <el-row :gutter="20">
              <el-col :span="6">
                <div class="stat-item">
                  <div class="stat-value">{{ totalTasks }}</div>
                  <div class="stat-label">总任务数</div>
                </div>
              </el-col>
              <el-col :span="6">
                <div class="stat-item">
                  <div class="stat-value">{{ completedTasks }}</div>
                  <div class="stat-label">已完成任务</div>
                </div>
              </el-col>
              <el-col :span="6">
                <div class="stat-item">
                  <div class="stat-value">{{ averageScore }}</div>
                  <div class="stat-label">平均分数</div>
                </div>
              </el-col>
              <el-col :span="6">
                <div class="stat-item">
                  <div class="stat-value">{{ highestScore }}</div>
                  <div class="stat-label">最高分数</div>
                </div>
              </el-col>
            </el-row>
          </el-card>
        </el-col>
      </el-row>
    </div>
  </el-card>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// 从localStorage读取训练报告数据
const loadReportFromStorage = () => {
  try {
    const storedReport = localStorage.getItem('trainingReport')
    if (storedReport) {
      const parsed = JSON.parse(storedReport)
      historyScores.value = parsed.historyScores || []
    }
  } catch (error) {
    console.error('Failed to load report from storage:', error)
  }
}

// 保存训练报告数据到localStorage
const saveReportToStorage = () => {
  try {
    const reportToSave = {
      historyScores: historyScores.value
    }
    localStorage.setItem('trainingReport', JSON.stringify(reportToSave))
  } catch (error) {
    console.error('Failed to save report to storage:', error)
  }
}

// 添加训练成绩
const addTrainingScore = (taskName, score) => {
  const today = new Date().toLocaleDateString()
  const newRecord = {
    date: today,
    task: taskName,
    score: score,
    level: score >= 80 ? '优秀' : (score >= 60 ? '良好' : '较差')
  }
  historyScores.value.unshift(newRecord)
  // 只保留最近10条记录
  if (historyScores.value.length > 10) {
    historyScores.value = historyScores.value.slice(0, 10)
  }
  saveReportToStorage()
}

// 暴露添加训练成绩函数到全局
window.addTrainingScore = addTrainingScore

const historyScores = ref([])

const totalTasks = computed(() => historyScores.value.length || 4)

const completedTasks = computed(() => {
  return historyScores.value.filter(item => item.score >= 80).length
})

const averageScore = computed(() => {
  if (historyScores.value.length === 0) return 0
  const sum = historyScores.value.reduce((acc, item) => acc + item.score, 0)
  return Math.round(sum / historyScores.value.length)
})

const highestScore = computed(() => {
  if (historyScores.value.length === 0) return 0
  return Math.max(...historyScores.value.map(item => item.score))
})

const getScoreColor = (score) => {
  if (score >= 80) return '#67C23A'
  if (score >= 60) return '#E6A23C'
  return '#F56C6C'
}

// 组件初始化时加载数据
onMounted(() => {
  loadReportFromStorage()
})
</script>

<style scoped>
.training-report-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.report-content {
  margin-top: 20px;
}

.report-card {
  margin-bottom: 20px;
}

.stat-item {
  text-align: center;
  padding: 20px;
  background-color: #f5f7fa;
  border-radius: 8px;
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
  color: #1890ff;
  margin-bottom: 5px;
}

.stat-label {
  font-size: 14px;
  color: #606266;
}

.progress-chart {
  height: 300px;
  position: relative;
}

.chart-container {
  height: 100%;
  position: relative;
}

.chart-axis {
  height: 100%;
  position: relative;
  border-left: 2px solid #e4e7ed;
  border-bottom: 2px solid #e4e7ed;
  margin: 20px;
}

.x-axis {
  position: absolute;
  bottom: -20px;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
}

.x-axis span {
  font-size: 12px;
  color: #909399;
}

.y-axis {
  position: absolute;
  top: 0;
  left: -30px;
  bottom: 0;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.y-axis span {
  font-size: 12px;
  color: #909399;
}

.chart-data {
  position: absolute;
  top: 20px;
  left: 20px;
  right: 20px;
  bottom: 20px;
}

.data-line {
  position: relative;
  height: 100%;
}

.data-point {
  position: absolute;
  width: 10px;
  height: 10px;
  background-color: #1890ff;
  border-radius: 50%;
  transform: translate(-50%, 50%);
}

.data-label {
  position: absolute;
  top: -20px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 12px;
  color: #606266;
  white-space: nowrap;
}

.data-line::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(to bottom, 
    rgba(24, 144, 255, 0.1) 0%, 
    rgba(24, 144, 255, 0) 100%);
  clip-path: polygon(0 100%, 20% 30%, 40% 20%, 60% 40%, 80% 25%, 100% 15%, 100% 100%);
}
</style>