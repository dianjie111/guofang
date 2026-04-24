<template>
  <el-card class="param-config-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>参数配置面板</span>
      </div>
    </template>
    <el-form :model="form" label-width="120px">
      <el-form-item label="频率">
        <el-select v-model="form.frequency" placeholder="选择频段">
          <el-option label="HF (高频)" value="hf"></el-option>
          <el-option label="VHF (甚高频)" value="vhf"></el-option>
          <el-option label="UHF (特高频)" value="uhf"></el-option>
        </el-select>
        <div class="param-hint" v-if="frequencyHint">
          <el-tag :type="frequencyHint.type">{{ frequencyHint.text }}</el-tag>
        </div>
      </el-form-item>
      <el-form-item label="加密方式">
        <el-select v-model="form.encryption" placeholder="选择加密方式">
          <el-option label="跳频" value="frequency-hopping"></el-option>
          <el-option label="扩频" value="spread-spectrum"></el-option>
          <el-option label="常规加密" value="normal"></el-option>
        </el-select>
        <div class="param-hint" v-if="encryptionHint">
          <el-tag :type="encryptionHint.type">{{ encryptionHint.text }}</el-tag>
        </div>
      </el-form-item>
      <el-form-item label="功率">
        <el-slider v-model="form.power" :min="1" :max="10" :step="1" show-input></el-slider>
        <div class="param-hint" v-if="powerHint">
          <el-tag :type="powerHint.type">{{ powerHint.text }}</el-tag>
        </div>
      </el-form-item>
      <el-form-item label="抗干扰模式">
        <el-select v-model="form.antiInterference" placeholder="选择抗干扰模式">
          <el-option label="自适应调频" value="adaptive-fm"></el-option>
          <el-option label="纠错编码" value="error-correction"></el-option>
          <el-option label="无" value="none"></el-option>
        </el-select>
        <div class="param-hint" v-if="antiInterferenceHint">
          <el-tag :type="antiInterferenceHint.type">{{ antiInterferenceHint.text }}</el-tag>
        </div>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script setup>
import { ref, computed, defineExpose } from 'vue'

const form = ref({
  frequency: 'vhf',
  encryption: 'frequency-hopping',
  power: 5,
  antiInterference: 'adaptive-fm'
})

const frequencyHint = computed(() => {
  switch (form.value.frequency) {
    case 'hf':
      return { type: 'info', text: 'HF: 适合远距离通信，受太阳活动影响' }
    case 'vhf':
      return { type: 'info', text: 'VHF: 适合近距离分队通信，抗干扰能力较好' }
    case 'uhf':
      return { type: 'info', text: 'UHF: 适合城市环境，带宽大、数据传输快' }
    default:
      return null
  }
})

const encryptionHint = computed(() => {
  switch (form.value.encryption) {
    case 'frequency-hopping':
      return { type: 'success', text: '跳频: 抗干扰能力强，适合强电磁干扰环境' }
    case 'spread-spectrum':
      return { type: 'success', text: '扩频: 低截获概率，适合隐蔽通信' }
    case 'normal':
      return { type: 'warning', text: '常规加密: 抗干扰能力较弱' }
    default:
      return null
  }
})

const powerHint = computed(() => {
  if (form.value.power < 3) {
    return { type: 'warning', text: '低功率: 通信距离受限' }
  } else if (form.value.power > 8) {
    return { type: 'warning', text: '高功率: 能耗较大' }
  } else {
    return { type: 'success', text: '功率适中: 平衡通信距离和能耗' }
  }
})

const antiInterferenceHint = computed(() => {
  switch (form.value.antiInterference) {
    case 'adaptive-fm':
      return { type: 'success', text: '自适应调频: 自动调整频率，抗干扰能力强' }
    case 'error-correction':
      return { type: 'success', text: '纠错编码: 提高数据传输可靠性' }
    case 'none':
      return { type: 'danger', text: '无: 抗干扰能力弱' }
    default:
      return null
  }
})

defineExpose({
  getParams: () => form.value,
  form
})
</script>

<style scoped>
.param-config-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.param-hint {
  margin-top: 8px;
}
</style>