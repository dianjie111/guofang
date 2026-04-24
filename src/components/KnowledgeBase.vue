<template>
  <el-card class="knowledge-base-card" shadow="hover">
    <template #header>
      <div class="card-header">
        <span>知识库</span>
      </div>
    </template>
    <div class="knowledge-content">
      <el-tabs v-model="activeTab">
        <el-tab-pane label="频段知识" name="frequency">
          <div class="knowledge-list">
            <el-card 
              v-for="item in frequencyKnowledge" 
              :key="item.id"
              class="knowledge-item"
            >
              <h3>{{ item.title }}</h3>
              <p class="knowledge-description">{{ item.description }}</p>
              <div class="knowledge-details">
                <p><strong>频率范围：</strong>{{ item.frequencyRange }}</p>
                <p><strong>传播特性：</strong>{{ item.propagation }}</p>
                <p><strong>适用场景：</strong>{{ item.scenario }}</p>
                <p><strong>优缺点：</strong>{{ item.advantages }} / {{ item.disadvantages }}</p>
              </div>
            </el-card>
          </div>
        </el-tab-pane>
        <el-tab-pane label="抗干扰技术" name="anti-interference">
          <div class="knowledge-list">
            <el-card 
              v-for="item in antiInterferenceKnowledge" 
              :key="item.id"
              class="knowledge-item"
            >
              <h3>{{ item.title }}</h3>
              <p class="knowledge-description">{{ item.description }}</p>
              <div class="knowledge-details">
                <p><strong>原理：</strong>{{ item.principle }}</p>
                <p><strong>效果：</strong>{{ item.effect }}</p>
                <p><strong>应用：</strong>{{ item.application }}</p>
              </div>
            </el-card>
          </div>
        </el-tab-pane>
        <el-tab-pane label="环境影响" name="environment">
          <div class="knowledge-list">
            <el-card 
              v-for="item in environmentKnowledge" 
              :key="item.id"
              class="knowledge-item"
            >
              <h3>{{ item.title }}</h3>
              <p class="knowledge-description">{{ item.description }}</p>
              <div class="knowledge-details">
                <p><strong>挑战：</strong>{{ item.challenges }}</p>
                <p><strong>推荐配置：</strong>{{ item.recommendedConfig }}</p>
                <p><strong>原理：</strong>{{ item.principle }}</p>
              </div>
            </el-card>
          </div>
        </el-tab-pane>
        <el-tab-pane label="真实案例" name="cases">
          <div class="knowledge-list">
            <el-card 
              v-for="item in caseKnowledge" 
              :key="item.id"
              class="knowledge-item"
            >
              <h3>{{ item.title }}</h3>
              <p class="knowledge-description">{{ item.description }}</p>
              <div class="knowledge-details">
                <p><strong>背景：</strong>{{ item.background }}</p>
                <p><strong>应用技术：</strong>{{ item.technologies }}</p>
                <p><strong>效果：</strong>{{ item.effect }}</p>
              </div>
            </el-card>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </el-card>
</template>

<script setup>
import { ref } from 'vue'

const activeTab = ref('frequency')

const frequencyKnowledge = [
  {
    id: 1,
    title: 'HF (高频)',
    description: 'HF频段是军事通信中常用的远距离通信手段',
    frequencyRange: '3-30MHz',
    propagation: '通过电离层反射，实现超视距传输',
    scenario: '远距离通信、跨山通信',
    advantages: '距离远、不依赖卫星',
    disadvantages: '数据速率低（可低至9.6kbps）、受太阳活动影响'
  },
  {
    id: 2,
    title: 'VHF (甚高频)',
    description: 'VHF频段适用于近距离分队通信',
    frequencyRange: '30-300MHz',
    propagation: '视距传播，直线传输',
    scenario: '近距离分队通信',
    advantages: '抗干扰能力较好',
    disadvantages: '受地形阻挡严重'
  },
  {
    id: 3,
    title: 'UHF (特高频)',
    description: 'UHF频段适用于城市环境和卫星通信',
    frequencyRange: '300-3000MHz',
    propagation: '视距传播，穿透能力强',
    scenario: '城市环境、卫星通信',
    advantages: '带宽大、数据传输快',
    disadvantages: '受建筑物影响较大'
  }
]

const antiInterferenceKnowledge = [
  {
    id: 1,
    title: '跳频（FHSS）',
    description: '跳频是一种有效的抗干扰技术',
    principle: '通信双方按照伪随机序列同步改变载波频率，每秒跳变可达20000跳以上',
    effect: '敌方不知道下一跳的频率，无法持续跟踪干扰',
    application: 'Link 16等军用数据链的核心技术'
  },
  {
    id: 2,
    title: '扩频（DSSS）',
    description: '扩频技术提高了通信的隐蔽性和抗干扰能力',
    principle: '将信号扩展到更宽的频带（远超传输所需的最小带宽），使信号"淹没"在噪声中',
    effect: '低截获概率（LPI/LPD），敌方难以发现你在通信',
    application: '隐蔽通信、抗多径干扰'
  },
  {
    id: 3,
    title: '自适应调零/抗干扰模式',
    description: '自适应调零技术提高了通信的抗干扰能力',
    principle: '采用定向天线将能量集中在窄波束内，或自动调整天线方向图，在干扰方向形成"零陷"',
    effect: '极大降低被敌方探测和干扰的概率',
    application: '隐身战机通信的关键技术'
  }
]

const environmentKnowledge = [
  {
    id: 1,
    title: '山地环境',
    description: '山地环境对通信的影响',
    challenges: '山体遮挡、视距阻断',
    recommendedConfig: 'HF频段',
    principle: 'HF通过电离层反射实现"绕射"，跨越山脊'
  },
  {
    id: 2,
    title: '城市环境',
    description: '城市环境对通信的影响',
    challenges: '建筑物阻挡、多径反射',
    recommendedConfig: 'UHF频段 + 扩频',
    principle: 'UHF穿透力强，扩频抗多径'
  },
  {
    id: 3,
    title: '强电磁干扰环境',
    description: '强电磁干扰环境对通信的影响',
    challenges: '敌方电子战压制',
    recommendedConfig: '跳频 + 抗干扰开启',
    principle: '跳频使敌方无法锁定频率'
  }
]

const caseKnowledge = [
  {
    id: 1,
    title: '第82空降师数字化火力演练',
    description: '通过战术可扩展移动自组网传输火力任务',
    background: '第82空降师进行数字化火力演练',
    technologies: '战术可扩展移动自组网',
    effect: '传输火力任务的速度比传统单信道地面与机载无线电系统调频传输快四倍'
  }
]
</script>

<style scoped>
.knowledge-base-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.knowledge-content {
  margin-top: 20px;
}

.knowledge-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.knowledge-item {
  border-left: 4px solid #1890ff;
  transition: all 0.3s ease;
}

.knowledge-item:hover {
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.knowledge-item h3 {
  margin: 0 0 10px 0;
  font-size: 16px;
  font-weight: 500;
  color: #1890ff;
}

.knowledge-description {
  margin: 0 0 15px 0;
  font-size: 14px;
  color: #606266;
}

.knowledge-details {
  font-size: 14px;
  color: #606266;
}

.knowledge-details p {
  margin: 5px 0;
}
</style>