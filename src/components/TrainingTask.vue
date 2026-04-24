<template>
  <el-card class="training-task-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>训练任务</span>
        <el-button type="info" size="small" @click="backToTaskList" v-if="currentTask">返回任务列表</el-button>
      </div>
    </template>
    
    <!-- 任务列表 -->
    <div class="task-list" v-if="!currentTask">
      <el-card 
        v-for="task in tasks" 
        :key="task.id"
        class="task-item"
        :class="{ 'task-completed': task.completed, 'task-in-progress': task.inProgress }"
      >
        <div class="task-header">
          <div class="task-title">
            <el-tag v-if="task.completed || task.inProgress" :type="task.statusType">{{ task.statusText }}</el-tag>
            <h3>{{ task.title }}</h3>
          </div>
          <el-button 
            type="primary" 
            size="small" 
            @click="startTask(task.id)"
            v-if="!task.completed && !task.inProgress"
          >
            开始任务
          </el-button>
          <el-button 
            type="success" 
            size="small" 
            @click="completeTask(task.id)"
            v-else-if="task.inProgress"
          >
            完成任务
          </el-button>
          <el-button 
            type="info" 
            size="small" 
            @click="viewTask(task.id)"
            v-else
          >
            查看详情
          </el-button>
        </div>
        <div class="task-content">
          <p class="task-description">{{ task.description }}</p>
          <p class="task-target">目标：{{ task.target }}</p>
          <p class="task-hint">提示：{{ task.hint }}</p>
        </div>
      </el-card>
    </div>
    
    <!-- 任务详情 -->
    <div class="task-detail" v-if="currentTask">
      <el-card class="detail-card">
        <div class="detail-header">
          <h2>{{ currentTask.title }}</h2>
          <el-tag v-if="currentTask.completed || currentTask.inProgress" :type="currentTask.statusType">{{ currentTask.statusText }}</el-tag>
        </div>
        <div class="detail-content">
          <el-divider>任务描述</el-divider>
          <p>{{ currentTask.description }}</p>
          
          <el-divider>任务目标</el-divider>
          <p>{{ currentTask.target }}</p>
          
          <el-divider>任务提示</el-divider>
          <p>{{ currentTask.hint }}</p>
          
          <el-divider>推荐配置</el-divider>
          <el-table :data="currentTask.recommendedConfig" style="width: 100%">
            <el-table-column prop="param" label="参数" width="120"></el-table-column>
            <el-table-column prop="value" label="推荐值"></el-table-column>
          </el-table>
          
          <el-divider>操作</el-divider>
          <div class="detail-actions">
            <el-button type="primary" @click="navigateToParamConfig">前往参数配置</el-button>
          </div>
        </div>
      </el-card>
    </div>
  </el-card>
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
          task.statusType = savedTask.completed ? 'success' : 'danger'
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
    environment: 'mountain', // 山地环境
    completed: false,
    inProgress: false,
    statusType: 'danger',
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
    environment: 'interference', // 强干扰环境
    completed: false,
    inProgress: false,
    statusType: 'danger',
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
    environment: 'city', // 城市环境
    completed: false,
    inProgress: false,
    statusType: 'danger',
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
    environment: 'city', // 城市环境（侦察任务通常在城市或复杂地形）
    completed: false,
    inProgress: false,
    statusType: 'danger',
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
    // 设置当前任务信息到全局
    window.currentTask = task
    window.currentTaskId = taskId
    // 隐藏侧边栏
    if (window.hideSidebar) {
      window.hideSidebar()
    }
  }
}

const completeTask = (taskId) => {
  const task = tasks.value.find(t => t.id === taskId)
  if (task) {
    task.completed = true
    task.inProgress = false
    task.statusType = 'success'
    task.statusText = '已完成'
  }
}

const viewTask = (taskId) => {
  const task = tasks.value.find(t => t.id === taskId)
  if (task) {
    currentTask.value = task
  }
}

const backToTaskList = () => {
  // 确保退出任务时任务状态不会变为已完成
  if (currentTask.value) {
    const task = tasks.value.find(t => t.id === currentTask.value.id)
    if (task) {
      task.inProgress = false
      task.statusType = 'danger'
      task.statusText = '未完成'
    }
  }
  // 清除全局任务信息
  window.currentTask = null
  window.currentTaskId = null
  currentTask.value = null
  // 显示侧边栏
  if (window.showSidebar) {
    window.showSidebar()
  }
}

// 完成当前任务
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
  // 排序任务：未完成的任务在前，已完成的任务在后
  tasks.value.sort((a, b) => {
    if (a.completed && !b.completed) return 1
    if (!a.completed && b.completed) return -1
    return 0
  })
  // 更新统计数据
  const completedCount = tasks.value.filter(t => t.completed).length
  if (window.updateTaskStats) {
    window.updateTaskStats(completedCount)
  }
  // 保存到localStorage
  saveTasksToStorage()
  backToTaskList()
}

// 暴露完成任务函数到全局
window.completeCurrentTask = completeCurrentTask

const navigateToParamConfig = () => {
  // 触发父组件切换到参数配置页面，并设置对应的环境
  if (window.switchToParamConfig && currentTask.value) {
    window.switchToParamConfig(currentTask.value.environment)
  }
}

// 组件初始化时加载任务状态
onMounted(() => {
  loadTasksFromStorage()
})

</script>

<style scoped>
.training-task-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.task-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.task-item {
  border-left: 4px solid #e4e7ed;
  transition: all 0.3s ease;
}

.task-item.task-completed {
  border-left-color: #67C23A;
  background-color: #f0f9eb;
}

.task-item.task-in-progress {
  border-left-color: #E6A23C;
  background-color: #fdf6ec;
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.task-title {
  display: flex;
  align-items: center;
  gap: 10px;
}

.task-title h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 500;
}

.task-content {
  margin-top: 10px;
}

.task-description {
  margin: 0 0 8px 0;
  font-size: 14px;
  color: #606266;
}

.task-target {
  margin: 0 0 8px 0;
  font-size: 14px;
  color: #606266;
}

.task-hint {
  margin: 0;
  font-size: 14px;
  color: #909399;
}
</style>