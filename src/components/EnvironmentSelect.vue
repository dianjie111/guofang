<template>
  <el-card class="environment-select-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>环境选择</span>
      </div>
    </template>
    <div class="environment-cards">
      <div 
        v-for="env in environments" 
        :key="env.value"
        class="environment-card"
        :class="{ active: selectedEnvironment === env.value }"
        @click="selectedEnvironment = env.value"
      >
        <div class="environment-icon" :style="{ backgroundColor: env.color }">
          {{ env.icon }}
        </div>
        <div class="environment-name">{{ env.name }}</div>
      </div>
    </div>
    <div class="environment-info" v-if="selectedEnvironment">
      <el-divider>环境信息</el-divider>
      <el-card :body-style="{ padding: '10px' }">
        <template v-if="selectedEnvironment === 'mountain'">
          <h4>山地环境</h4>
          <p>特点：地形复杂，遮挡多，通信距离受限</p>
          <p>建议：使用较高功率，选择合适频段</p>
        </template>
        <template v-else-if="selectedEnvironment === 'city'">
          <h4>城市环境</h4>
          <p>特点：建筑物密集，信号反射多，干扰源多</p>
          <p>建议：使用抗干扰技术，合理选择频率</p>
        </template>
        <template v-else-if="selectedEnvironment === 'interference'">
          <h4>强电磁干扰环境</h4>
          <p>特点：电磁环境复杂，干扰强度大</p>
          <p>建议：使用跳频、扩频等抗干扰技术，提高功率</p>
        </template>
      </el-card>
    </div>
  </el-card>
</template>

<script setup>
import { ref, defineExpose } from 'vue'

const selectedEnvironment = ref('mountain')

const environments = [
  { value: 'mountain', name: '山地', icon: '🏔️', color: '#4CAF50' },
  { value: 'city', name: '城市', icon: '🏙️', color: '#9E9E9E' },
  { value: 'interference', name: '强电磁干扰', icon: '⚡', color: '#F44336' }
]

defineExpose({
  getEnvironment: () => selectedEnvironment.value,
  setEnvironment: (env) => { selectedEnvironment.value = env }
})
</script>

<style scoped>
.environment-select-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.environment-cards {
  display: flex;
  gap: 20px;
  margin-bottom: 20px;
}

.environment-card {
  flex: 1;
  padding: 20px;
  border: 2px solid #e4e7ed;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  text-align: center;
}

.environment-card:hover {
  border-color: #409EFF;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.environment-card.active {
  border-color: #409EFF;
  background-color: #ecf5ff;
}

.environment-icon {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 10px;
  font-size: 24px;
}

.environment-name {
  font-size: 14px;
  font-weight: 500;
}

.environment-info {
  margin-top: 20px;
}
</style>