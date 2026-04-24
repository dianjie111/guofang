<template>
  <el-card class="system-settings-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>系统设置</span>
      </div>
    </template>
    <div class="settings-content">
      <el-form :model="settings" label-width="120px">
        <el-form-item label="主题">
          <el-radio-group v-model="settings.theme">
            <el-radio-button label="light">浅色</el-radio-button>
            <el-radio-button label="dark">深色</el-radio-button>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="声音">
          <el-switch v-model="settings.sound" active-text="开启" inactive-text="关闭"></el-switch>
        </el-form-item>
        <el-form-item label="音效音量">
          <el-slider v-model="settings.volume" :min="0" :max="100" :step="1" show-input></el-slider>
        </el-form-item>
        <el-form-item label="数据管理">
          <el-button type="danger" @click="resetData">重置数据</el-button>
          <el-button type="primary" @click="exportData">导出数据</el-button>
        </el-form-item>
        <el-form-item label="系统信息">
          <el-card :body-style="{ padding: '10px' }">
            <p>版本：v1.0.0</p>
            <p>开发者：通信模拟训练团队</p>
            <p>最后更新：2026-04-23</p>
          </el-card>
        </el-form-item>
      </el-form>
    </div>
  </el-card>
</template>

<script setup>
import { ref } from 'vue'

const settings = ref({
  theme: 'light',
  sound: true,
  volume: 70
})

const resetData = () => {
  // 重置数据
  if (confirm('确定要重置所有数据吗？')) {
    // 执行重置操作
    console.log('重置数据')
  }
}

const exportData = () => {
  // 导出数据
  console.log('导出数据')
}
</script>

<style scoped>
.system-settings-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.settings-content {
  margin-top: 20px;
}
</style>