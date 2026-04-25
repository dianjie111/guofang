<template>
  <div class="app-container">
    <div class="noise-overlay"></div>
    <el-container style="height: 100vh;">
      <el-aside width="260px" class="sidebar" v-show="sidebarVisible">
        <div class="sidebar-header">
          <div class="logo-section">
            <div class="logo-icon">⚡</div>
            <div class="logo-text">
              <h2>通信模拟器</h2>
              <span class="logo-subtitle">TACTICAL COMM</span>
            </div>
          </div>
        </div>
        <div class="sidebar-content">
          <el-menu
            :default-active="activeMenu"
            class="sidebar-menu"
            @select="handleMenuSelect"
          >
            <el-menu-item index="home">
              <span class="menu-icon">🏠</span>
              <span>指挥中心</span>
            </el-menu-item>
            <el-menu-item index="training-task">
              <span class="menu-icon">📋</span>
              <span>任务区域</span>
            </el-menu-item>
            <el-menu-item index="training-report">
              <span class="menu-icon">📊</span>
              <span>战报分析</span>
            </el-menu-item>
            <el-menu-item index="knowledge-base">
              <span class="menu-icon">📚</span>
              <span>战术库</span>
            </el-menu-item>

          </el-menu>
        </div>
        <div class="sidebar-footer">
          <div class="status-indicator">
            <span class="status-dot"></span>
            <span class="status-text">系统在线</span>
          </div>
        </div>
      </el-aside>
      <el-container>
        <el-header height="70px" class="header">
          <div class="header-left">
            <div v-if="!sidebarVisible" class="menu-toggle" @click="showSidebar">
              <span>☰</span>
            </div>
            <h1>
              <span class="title-accent">//</span>
              通信参数配置训练模拟器
              <span class="title-accent">//</span>
            </h1>
          </div>
          <div class="system-status">
            <div class="status-block">
              <span class="status-label">系统状态</span>
              <el-tag type="success" class="custom-tag">正常运行</el-tag>
            </div>
            <div class="status-block">
              <span class="status-label">当前时间</span>
              <el-tag type="info" class="custom-tag">{{ currentTime }}</el-tag>
            </div>
          </div>
        </el-header>
        <el-main>
          <div class="main-content">
            <!-- 首页 -->
            <div v-if="activeMenu === 'home'" class="page-transition">
              <HomePage />
            </div>
            <!-- 参数配置 -->
            <div v-else-if="activeMenu === 'param-config'" class="page-transition">
              <el-row :gutter="24">
                <el-col :span="9">
                  <ParamConfig ref="paramConfigRef" />
                  <EnvironmentSelect ref="environmentSelectRef" />
                </el-col>
                <el-col :span="15">
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
            <div v-else-if="activeMenu === 'training-task'" class="page-transition">
              <TrainingTask />
            </div>
            <!-- 训练报告 -->
            <div v-else-if="activeMenu === 'training-report'" class="page-transition">
              <TrainingReport />
            </div>
            <!-- 知识库 -->
            <div v-else-if="activeMenu === 'knowledge-base'" class="page-transition">
              <KnowledgeBase />
            </div>

          </div>
        </el-main>
        <el-footer height="50px" class="footer">
          <div class="footer-content">
            <span class="footer-left">◆ 科技守卫家园 ◆</span>
            <span class="footer-right">军事通信保障训练系统 v2.0</span>
          </div>
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
  currentTime.value = now.toLocaleString('zh-CN', { 
    hour12: false,
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit'
  })
}

const handleMenuSelect = (key) => {
  activeMenu.value = key
  if (key === 'param-config') {
    sidebarVisible.value = false
  } else {
    sidebarVisible.value = true
  }
}

window.switchToParamConfig = (env) => {
  activeMenu.value = 'param-config'
  sidebarVisible.value = false
  if (env && environmentSelectRef.value) {
    environmentSelectRef.value.setEnvironment(env)
  }
}

window.goToTask = (taskId) => {
  activeMenu.value = 'training-task'
}

window.goToKnowledge = (tab, articleId) => {
  activeMenu.value = 'knowledge-base'
}

window.switchToTrainingTask = () => {
  activeMenu.value = 'training-task'
}

window.switchToHome = () => {
  activeMenu.value = 'home'
  sidebarVisible.value = true
}

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
  font-family: 'Courier New', 'Microsoft YaHei', monospace;
  background: #0a0e17;
  color: #e0e6ed;
  overflow: hidden;
}

.app-container {
  height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
}

.noise-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  opacity: 0.03;
  z-index: 9999;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
}

.sidebar {
  background: linear-gradient(180deg, #0d1117 0%, #0a0e14 100%);
  border-right: 1px solid #1f2937;
  display: flex;
  flex-direction: column;
  box-shadow: 4px 0 20px rgba(0, 0, 0, 0.5);
  position: relative;
}

.sidebar::before {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  width: 1px;
  height: 100%;
  background: linear-gradient(180deg, transparent 0%, #00ff88 50%, transparent 100%);
  opacity: 0.3;
}

.sidebar-header {
  padding: 24px 20px;
  border-bottom: 1px solid #1f2937;
  background: linear-gradient(135deg, #0d1117 0%, #0a0e14 100%);
}

.logo-section {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo-icon {
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, #00ff88 0%, #00cc6a 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  box-shadow: 0 0 20px rgba(0, 255, 136, 0.3);
  animation: pulse-glow 2s ease-in-out infinite;
}

@keyframes pulse-glow {
  0%, 100% { box-shadow: 0 0 20px rgba(0, 255, 136, 0.3); }
  50% { box-shadow: 0 0 30px rgba(0, 255, 136, 0.5); }
}

.logo-text h2 {
  font-size: 18px;
  font-weight: 700;
  margin: 0;
  background: linear-gradient(90deg, #00ff88 0%, #00d4ff 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: 1px;
}

.logo-subtitle {
  font-size: 10px;
  color: #4b5563;
  letter-spacing: 3px;
  font-weight: 600;
}

.sidebar-content {
  flex: 1;
  padding: 20px 12px;
}

.sidebar-menu {
  background: transparent;
  border-right: none;
}

.sidebar-menu .el-menu-item {
  color: #9ca3af;
  height: 56px;
  line-height: 56px;
  margin: 8px 0;
  border-radius: 10px;
  font-size: 14px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  align-items: center;
  gap: 12px;
  position: relative;
  overflow: hidden;
}

.sidebar-menu .el-menu-item::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 3px;
  background: #00ff88;
  transform: scaleY(0);
  transition: transform 0.3s ease;
}

.sidebar-menu .el-menu-item:hover {
  background: rgba(0, 255, 136, 0.08);
  color: #00ff88;
}

.sidebar-menu .el-menu-item:hover::before {
  transform: scaleY(1);
}

.sidebar-menu .el-menu-item.is-active {
  background: linear-gradient(90deg, rgba(0, 255, 136, 0.15) 0%, transparent 100%);
  color: #00ff88;
  font-weight: 600;
}

.sidebar-menu .el-menu-item.is-active::before {
  transform: scaleY(1);
}

.menu-icon {
  font-size: 18px;
}

.sidebar-footer {
  padding: 20px;
  border-top: 1px solid #1f2937;
}

.status-indicator {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  background: rgba(0, 255, 136, 0.05);
  border-radius: 10px;
  border: 1px solid rgba(0, 255, 136, 0.1);
}

.status-dot {
  width: 10px;
  height: 10px;
  background: #00ff88;
  border-radius: 50%;
  animation: blink 1.5s ease-in-out infinite;
  box-shadow: 0 0 10px #00ff88;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.4; }
}

.status-text {
  font-size: 13px;
  color: #00ff88;
  font-weight: 500;
}

.header {
  background: linear-gradient(90deg, #0d1117 0%, #0a0e14 50%, #0d1117 100%);
  border-bottom: 1px solid #1f2937;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 32px;
  position: relative;
}

.header::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 1px;
  background: linear-gradient(90deg, transparent 0%, #00ff88 20%, #00d4ff 50%, #00ff88 80%, transparent 100%);
}

.header-left {
  display: flex;
  align-items: center;
  gap: 16px;
}

.menu-toggle {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 255, 136, 0.1);
  border: 1px solid rgba(0, 255, 136, 0.2);
  border-radius: 8px;
  cursor: pointer;
  font-size: 20px;
  transition: all 0.3s ease;
}

.menu-toggle:hover {
  background: rgba(0, 255, 136, 0.2);
}

.header h1 {
  font-size: 22px;
  font-weight: 700;
  letter-spacing: 2px;
  margin: 0;
}

.title-accent {
  color: #00ff88;
}

.system-status {
  display: flex;
  gap: 16px;
}

.status-block {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.status-label {
  font-size: 11px;
  color: #4b5563;
  letter-spacing: 1px;
}

.custom-tag {
  font-family: 'Courier New', monospace;
  font-size: 13px;
  padding: 6px 12px;
  border-radius: 6px;
  border: none;
}

.main-content {
  padding: 24px 32px;
  overflow-y: auto;
  height: calc(100vh - 120px);
}

.page-transition {
  animation: fadeInUp 0.5s ease-out;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.footer {
  background: linear-gradient(90deg, #0d1117 0%, #0a0e14 50%, #0d1117 100%);
  border-top: 1px solid #1f2937;
  display: flex;
  align-items: center;
  justify-content: center;
}

.footer-content {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 32px;
}

.footer-left {
  font-size: 13px;
  color: #00ff88;
  font-weight: 600;
  letter-spacing: 2px;
}

.footer-right {
  font-size: 12px;
  color: #4b5563;
  letter-spacing: 1px;
}
</style>