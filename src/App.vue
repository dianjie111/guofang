<template>
  <div class="app-container">
    <el-container style="height: 100vh;">
      <el-aside width="200px" class="sidebar" v-show="sidebarVisible">
        <div class="sidebar-header">
          <h2>通信模拟器</h2>
        </div>
        <el-menu
          :default-active="activeMenu"
          class="sidebar-menu"
          @select="handleMenuSelect"
        >
          <el-menu-item index="home">
            <span>首页</span>
          </el-menu-item>
          <el-menu-item index="training-task">
            <span>训练任务</span>
          </el-menu-item>
          <el-menu-item index="training-report">
            <span>训练报告</span>
          </el-menu-item>
          <el-menu-item index="knowledge-base">
            <span>知识库</span>
          </el-menu-item>
          <el-menu-item index="system-settings">
            <span>系统设置</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header height="60px" class="header">
          <h1>通信参数配置训练模拟器</h1>
          <div class="system-status">
            <el-tag type="success">系统正常</el-tag>
            <el-tag type="info">{{ currentTime }}</el-tag>
          </div>
        </el-header>
        <el-main>
          <div class="main-content">
            <!-- 首页 -->
            <div v-if="activeMenu === 'home'">
              <HomePage />
            </div>
            <!-- 参数配置 -->
            <div v-else-if="activeMenu === 'param-config'">
              <el-row :gutter="20">
                <el-col :span="10">
                  <ParamConfig ref="paramConfigRef" />
                  <EnvironmentSelect ref="environmentSelectRef" />
                </el-col>
                <el-col :span="14">
                  <QualityAssessment 
                    :paramConfigRef="paramConfigRef"
                    :environmentSelectRef="environmentSelectRef"
                  />
                  <KnowledgeTip 
                    :paramConfigRef="paramConfigRef"
                    :environmentSelectRef="environmentSelectRef"
                  />
                </el-col>
              </el-row>
            </div>
            <!-- 训练任务 -->
            <div v-else-if="activeMenu === 'training-task'">
              <TrainingTask />
            </div>
            <!-- 训练报告 -->
            <div v-else-if="activeMenu === 'training-report'">
              <TrainingReport />
            </div>
            <!-- 知识库 -->
            <div v-else-if="activeMenu === 'knowledge-base'">
              <KnowledgeBase />
            </div>
            <!-- 系统设置 -->
            <div v-else-if="activeMenu === 'system-settings'">
              <SystemSettings />
            </div>
          </div>
        </el-main>
        <el-footer height="40px" class="footer">
          <p>科技守卫家园 - 军事通信保障训练系统</p>
        </el-footer>
      </el-container>
    </el-container>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'

import ParamConfig from './components/ParamConfig.vue'
import EnvironmentSelect from './components/EnvironmentSelect.vue'
import QualityAssessment from './components/QualityAssessment.vue'
import TaskSimulation from './components/TaskSimulation.vue'
import KnowledgeTip from './components/KnowledgeTip.vue'
import HistoryRecord from './components/HistoryRecord.vue'
import TrainingTask from './components/TrainingTask.vue'
import TrainingReport from './components/TrainingReport.vue'
import KnowledgeBase from './components/KnowledgeBase.vue'
import SystemSettings from './components/SystemSettings.vue'
import HomePage from './components/HomePage.vue'

const paramConfigRef = ref(null)
const environmentSelectRef = ref(null)
const historyRecordRef = ref(null)
const currentTime = ref('')
const activeMenu = ref('home')
const sidebarVisible = ref(true)

let timer = null

const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleString()
}

const handleMenuSelect = (key) => {
  activeMenu.value = key
  // 如果进入参数配置页面，隐藏侧边栏
  if (key === 'param-config') {
    sidebarVisible.value = false
  } else {
    // 其他页面显示侧边栏
    sidebarVisible.value = true
  }
}

// 暴露切换到参数配置页面的函数
window.switchToParamConfig = (env) => {
  activeMenu.value = 'param-config'
  // 隐藏侧边栏
  sidebarVisible.value = false
  // 如果传入了环境参数，设置环境
  if (env && environmentSelectRef.value) {
    environmentSelectRef.value.setEnvironment(env)
  }
}

// 暴露跳转到训练任务的函数
window.goToTask = (taskId) => {
  activeMenu.value = 'training-task'
}

// 暴露跳转到知识库的函数
window.goToKnowledge = (tab, articleId) => {
  activeMenu.value = 'knowledge-base'
}

// 暴露切换到训练任务页面的函数
window.switchToTrainingTask = () => {
  activeMenu.value = 'training-task'
}

// 暴露切换到首页的函数
window.switchToHome = () => {
  activeMenu.value = 'home'
  // 显示侧边栏
  sidebarVisible.value = true
}

// 暴露控制侧边栏的函数
window.hideSidebar = () => {
  sidebarVisible.value = false
}

window.showSidebar = () => {
  sidebarVisible.value = true
}

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})


</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f5f7fa;
}

.app-container {
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.sidebar {
  background-color: #303133;
  color: white;
  display: flex;
  flex-direction: column;
  box-shadow: 2px 0 6px rgba(0, 21, 41, 0.35);
}

.sidebar-header {
  padding: 17px 20px;
  border-bottom: 1px solid #404040;
  background-color: #262626;
}

.sidebar-header h2 {
  font-size: 16px;
  font-weight: bold;
  margin: 0;
  text-align: center;
  color: #409EFF;
}

.sidebar-menu {
  flex: 1;
  background-color: #303133;
  border-right: none;
}

.sidebar-menu .el-menu-item {
  color: #ccc;
  height: 50px;
  line-height: 50px;
  margin: 12px 15px;
  border-radius: 4px;
  font-size: 14px;
  transition: all 0.3s ease;
}

.sidebar-menu .el-menu-item:hover {
  background-color: rgba(64, 158, 255, 0.2);
  color: white;
}

.sidebar-menu .el-menu-item.is-active {
  background-color: #409EFF;
  color: white;
  font-weight: 500;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.sidebar-menu .el-menu-item.is-active:hover {
  background-color: #66b1ff;
}

.el-menu {
  border-right: none;
}



.header {
  background-color: #1890ff;
  color: white;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.header h1 {
  font-size: 24px;
  font-weight: bold;
}

.system-status {
  display: flex;
  gap: 10px;
}

.main-content {
  padding: 20px;
  overflow-y: auto;
}

.footer {
  background-color: #f0f2f5;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  color: #606266;
}
</style>