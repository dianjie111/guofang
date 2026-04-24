<template>
  <el-card class="task-simulation-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>任务场景模拟</span>
      </div>
    </template>
    <el-select v-model="selectedTask" placeholder="选择任务场景">
      <el-option label="执行侦察任务，需隐蔽通信" value="reconnaissance"></el-option>
      <el-option label="执行攻击任务，需高速通信" value="attack"></el-option>
      <el-option label="执行救援任务，需稳定通信" value="rescue"></el-option>
    </el-select>
    <div class="task-info" v-if="selectedTask">
      <el-divider>任务要求</el-divider>
      <el-card :body-style="{ padding: '10px' }">
        <template v-if="selectedTask === 'reconnaissance'">
          <h4>侦察任务</h4>
          <p>要求：隐蔽通信，避免被敌方截获</p>
          <p>建议：使用跳频、扩频技术，降低功率，选择合适频段</p>
        </template>
        <template v-else-if="selectedTask === 'attack'">
          <h4>攻击任务</h4>
          <p>要求：高速通信，确保命令及时传达</p>
          <p>建议：使用较高功率，选择视距传播频段，确保通信质量</p>
        </template>
        <template v-else-if="selectedTask === 'rescue'">
          <h4>救援任务</h4>
          <p>要求：稳定通信，确保信息准确传递</p>
          <p>建议：使用抗干扰技术，选择稳定频段，合理调整功率</p>
        </template>
      </el-card>
    </div>
  </el-card>
</template>

<script setup>
import { ref } from 'vue'

const selectedTask = ref('')
</script>

<style scoped>
.task-simulation-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.task-info {
  margin-top: 20px;
}
</style>