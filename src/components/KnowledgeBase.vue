<template>
  <div class="knowledge-base-page">
    <div class="page-header">
      <div class="header-badge">
        <span class="badge-icon">📚</span>
        <span class="badge-text">战术知识库</span>
      </div>
      <h1 class="page-title">通信技术知识库</h1>
      <p class="page-subtitle">深入了解军事通信技术的核心原理与实战应用</p>
    </div>

    <div class="tabs-container">
      <button
        v-for="tab in tabs"
        :key="tab.value"
        class="tab-button"
        :class="{ active: activeTab === tab.value }"
        @click="activeTab = tab.value"
      >
        <span class="tab-icon">{{ tab.icon }}</span>
        <span class="tab-label">{{ tab.label }}</span>
      </button>
    </div>

    <div class="content-container">
      <!-- 频段知识 -->
      <div v-if="activeTab === 'frequency'" class="knowledge-section">
        <div class="section-intro">
          <div class="intro-icon">📡</div>
          <div class="intro-text">
            <h2>频段知识</h2>
            <p>了解不同频段的特性与适用场景，为作战环境选择最佳通信频率</p>
          </div>
        </div>
        <div class="knowledge-grid">
          <article
            v-for="item in frequencyKnowledge"
            :key="item.id"
            class="knowledge-card"
          >
            <div class="card-header">
              <div class="card-icon">{{ item.icon }}</div>
              <h3 class="card-title">{{ item.title }}</h3>
              <div class="freq-badge">{{ item.frequencyRange }}</div>
            </div>
            <p class="card-description">{{ item.description }}</p>
            <div class="card-details">
              <div class="detail-item">
                <span class="detail-label">传播特性</span>
                <span class="detail-value">{{ item.propagation }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">适用场景</span>
                <span class="detail-value">{{ item.scenario }}</span>
              </div>
              <div class="pros-cons">
                <div class="pros">
                  <span class="pros-icon">✅</span>
                  <span class="pros-text">{{ item.advantages }}</span>
                </div>
                <div class="cons">
                  <span class="cons-icon">⚠️</span>
                  <span class="cons-text">{{ item.disadvantages }}</span>
                </div>
              </div>
            </div>
          </article>
        </div>
      </div>

      <!-- 抗干扰技术 -->
      <div v-if="activeTab === 'anti-interference'" class="knowledge-section">
        <div class="section-intro">
          <div class="intro-icon">🛡️</div>
          <div class="intro-text">
            <h2>抗干扰技术</h2>
            <p>掌握现代军事通信的抗干扰技术，确保复杂电磁环境下的通信可靠</p>
          </div>
        </div>
        <div class="knowledge-grid">
          <article
            v-for="item in antiInterferenceKnowledge"
            :key="item.id"
            class="knowledge-card accent"
          >
            <div class="card-header">
              <div class="card-icon">{{ item.icon }}</div>
              <h3 class="card-title">{{ item.title }}</h3>
              <div class="tech-badge">核心技术</div>
            </div>
            <p class="card-description">{{ item.description }}</p>
            <div class="card-details">
              <div class="detail-item">
                <span class="detail-label">技术原理</span>
                <span class="detail-value">{{ item.principle }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">效果</span>
                <span class="detail-value">{{ item.effect }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">实战应用</span>
                <span class="detail-value">{{ item.application }}</span>
              </div>
            </div>
          </article>
        </div>
      </div>

      <!-- 环境影响 -->
      <div v-if="activeTab === 'environment'" class="knowledge-section">
        <div class="section-intro">
          <div class="intro-icon">🌍</div>
          <div class="intro-text">
            <h2>环境影响</h2>
            <p>了解不同作战环境对通信的影响，制定针对性的通信方案</p>
          </div>
        </div>
        <div class="knowledge-grid">
          <article
            v-for="item in environmentKnowledge"
            :key="item.id"
            class="knowledge-card"
          >
            <div class="card-header">
              <div class="card-icon">{{ item.icon }}</div>
              <h3 class="card-title">{{ item.title }}</h3>
              <div class="env-badge">{{ item.type }}</div>
            </div>
            <p class="card-description">{{ item.description }}</p>
            <div class="card-details">
              <div class="detail-item challenge">
                <span class="detail-label">主要挑战</span>
                <span class="detail-value">{{ item.challenges }}</span>
              </div>
              <div class="detail-item solution">
                <span class="detail-label">推荐配置</span>
                <span class="detail-value">{{ item.recommendedConfig }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">技术原理</span>
                <span class="detail-value">{{ item.principle }}</span>
              </div>
            </div>
          </article>
        </div>
      </div>

      <!-- 真实案例 -->
      <div v-if="activeTab === 'cases'" class="knowledge-section">
        <div class="section-intro">
          <div class="intro-icon">🎯</div>
          <div class="intro-text">
            <h2>真实案例</h2>
            <p>学习真实战例中的通信技术应用，提升实战能力</p>
          </div>
        </div>
        <div class="knowledge-grid">
          <article
            v-for="item in caseKnowledge"
            :key="item.id"
            class="knowledge-card featured"
          >
            <div class="featured-badge">战例分析</div>
            <div class="card-header">
              <div class="card-icon">{{ item.icon }}</div>
              <h3 class="card-title">{{ item.title }}</h3>
            </div>
            <p class="card-description">{{ item.description }}</p>
            <div class="card-details">
              <div class="detail-item">
                <span class="detail-label">作战背景</span>
                <span class="detail-value">{{ item.background }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">应用技术</span>
                <span class="detail-value">{{ item.technologies }}</span>
              </div>
              <div class="detail-item highlight">
                <span class="detail-label">作战效果</span>
                <span class="detail-value">{{ item.effect }}</span>
              </div>
            </div>
          </article>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const activeTab = ref('frequency')

const tabs = [
  {
    value: 'frequency',
    label: '频段知识',
    icon: '📡'
  },
  {
    value: 'anti-interference',
    label: '抗干扰技术',
    icon: '🛡️'
  },
  {
    value: 'environment',
    label: '环境影响',
    icon: '🌍'
  },
  {
    value: 'cases',
    label: '真实案例',
    icon: '🎯'
  }
]

const frequencyKnowledge = [
  {
    id: 1,
    title: 'HF (高频)',
    icon: '🏔️',
    description: 'HF频段是军事通信中常用的远距离通信手段，不依赖基础设施',
    frequencyRange: '3-30MHz',
    propagation: '通过电离层反射，实现超视距传输',
    scenario: '远距离通信、跨山通信、战略通信',
    advantages: '通信距离远、不依赖卫星、基础设施',
    disadvantages: '数据速率低（可低至9.6kbps）、受太阳活动影响大'
  },
  {
    id: 2,
    title: 'VHF (甚高频)',
    icon: '📻',
    description: 'VHF频段适用于近距离分队通信，是班排级战术通信主力',
    frequencyRange: '30-300MHz',
    propagation: '视距传播，直线传输',
    scenario: '近距离分队通信、战术协同',
    advantages: '抗干扰能力较好、设备小型化',
    disadvantages: '受地形遮挡严重、通信距离有限'
  },
  {
    id: 3,
    title: 'UHF (特高频)',
    icon: '🏙️',
    description: 'UHF频段适用于城市环境和卫星通信，数据传输能力强',
    frequencyRange: '300-3000MHz',
    propagation: '视距传播，穿透能力强',
    scenario: '城市环境、卫星通信、数据传输',
    advantages: '带宽大、数据传输快、穿透能力强',
    disadvantages: '受建筑物影响较大、通信距离近'
  }
]

const antiInterferenceKnowledge = [
  {
    id: 1,
    title: '跳频（FHSS）',
    icon: '🔄',
    description: '跳频是现代军事通信最有效的抗干扰技术之一',
    principle: '通信双方按照伪随机序列同步改变载波频率，每秒跳变可达20000跳以上',
    effect: '敌方不知道下一跳的频率，无法持续跟踪干扰',
    application: 'Link 16等军用数据链的核心技术'
  },
  {
    id: 2,
    title: '扩频（DSSS）',
    icon: '🔇',
    description: '扩频技术大幅提高了通信的隐蔽性和抗干扰能力',
    principle: '将信号扩展到更宽的频带（远超传输所需的最小带宽），使信号"淹没"在噪声中',
    effect: '低截获概率（LPI/LPD），敌方难以发现你在通信',
    application: '隐蔽通信、抗多径干扰、特种作战'
  },
  {
    id: 3,
    title: '自适应调零',
    icon: '🎯',
    description: '自适应调零技术是现代雷达和通信系统的高级抗干扰手段',
    principle: '采用定向天线将能量集中在窄波束内，或自动调整天线方向图，在干扰方向形成"零陷"',
    effect: '极大降低被敌方探测和干扰的概率',
    application: '隐身战机通信的关键技术、高级指挥系统'
  }
]

const environmentKnowledge = [
  {
    id: 1,
    title: '山地环境',
    icon: '⛰️',
    type: '复杂地形',
    description: '山地环境对通信系统提出了严峻挑战，需要特殊的技术方案',
    challenges: '山体遮挡、视距阻断、地形复杂、信号衰弱',
    recommendedConfig: 'HF频段 + 适当功率 + 高天线',
    principle: 'HF通过电离层反射实现"绕射"，跨越山脊障碍'
  },
  {
    id: 2,
    title: '城市环境',
    icon: '🏢',
    type: '城市作战',
    description: '城市环境信号反射多、干扰源密集，需要针对性的通信策略',
    challenges: '建筑物遮挡、多径反射、电磁干扰、信号衰减',
    recommendedConfig: 'UHF频段 + 扩频技术 + 纠错编码',
    principle: 'UHF穿透能力强，扩频技术有效对抗多径效应'
  },
  {
    id: 3,
    title: '强电磁干扰环境',
    icon: '⚡',
    type: '电子战',
    description: '敌方电子战压制下，通信系统必须具备强大的抗干扰能力',
    challenges: '敌方电子战压制、频谱干扰、信号侦听',
    recommendedConfig: '跳频 + 自适应调频 + 低功率',
    principle: '跳频使敌方无法锁定频率，自适应调频实时规避干扰'
  }
]

const caseKnowledge = [
  {
    id: 1,
    title: '第82空降师数字化火力演练',
    icon: '🎖️',
    description: '美军第82空降师在实战演练中展示了现代通信技术的威力',
    background: '第82空降师进行数字化火力支援演练，检验新型通信系统',
    technologies: '战术可扩展移动自组网、先进数据链、卫星通信',
    effect: '传输火力任务的速度比传统单信道地面与机载无线电系统调频传输快四倍'
  }
]
</script>

<style scoped>
.knowledge-base-page {
  padding: 0;
}

.page-header {
  margin-bottom: 32px;
  text-align: left;
}

.header-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 14px;
  background: rgba(0, 255, 136, 0.1);
  border: 1px solid rgba(0, 255, 136, 0.2);
  border-radius: 50px;
  margin-bottom: 16px;
}

.badge-icon {
  color: #00ff88;
  font-size: 14px;
}

.badge-text {
  font-size: 11px;
  color: #00ff88;
  letter-spacing: 2px;
  font-weight: 600;
}

.page-title {
  font-size: 32px;
  font-weight: 800;
  margin: 0 0 8px 0;
  letter-spacing: 1px;
}

.page-subtitle {
  font-size: 14px;
  color: #6b7280;
  margin: 0;
}

.tabs-container {
  display: flex;
  gap: 12px;
  margin-bottom: 28px;
  padding: 8px;
  background: rgba(13, 17, 23, 0.8);
  border-radius: 12px;
  border: 1px solid #1f2937;
}

.tab-button {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 14px 20px;
  background: transparent;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #9ca3af;
}

.tab-button:hover {
  background: rgba(31, 41, 55, 0.6);
  color: #e0e6ed;
}

.tab-button.active {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  color: #00ff88;
}

.tab-icon {
  font-size: 20px;
}

.tab-label {
  font-size: 14px;
  font-weight: 600;
}

.content-container {
  min-height: 400px;
}

.knowledge-section {
  animation: fadeIn 0.4s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.section-intro {
  display: flex;
  align-items: flex-start;
  gap: 16px;
  margin-bottom: 28px;
  padding: 20px;
  background: #0a0e17;
  border: 1px solid #1f2937;
  border-radius: 16px;
}

.intro-icon {
  width: 56px;
  height: 56px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  flex-shrink: 0;
}

.intro-text h2 {
  font-size: 20px;
  font-weight: 700;
  margin: 0 0 6px 0;
}

.intro-text p {
  font-size: 14px;
  color: #6b7280;
  margin: 0;
}

.knowledge-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(360px, 1fr));
  gap: 20px;
}

.knowledge-card {
  background: #0a0e17;
  border: 1px solid #1f2937;
  border-radius: 16px;
  padding: 24px;
  position: relative;
  overflow: hidden;
  transition: all 0.4s ease;
}

.knowledge-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: linear-gradient(180deg, #00ff88 0%, #00d4ff 100%);
  opacity: 0.5;
}

.knowledge-card.accent::before {
  background: linear-gradient(180deg, #ffa500 0%, #ff6b6b 100%);
}

.knowledge-card.featured {
  grid-column: 1 / -1;
  border-color: rgba(0, 255, 136, 0.3);
}

.knowledge-card:hover {
  transform: translateY(-4px);
  border-color: #374151;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.3);
}

.featured-badge {
  position: absolute;
  top: 16px;
  right: 16px;
  padding: 6px 14px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.2) 0%, rgba(0, 212, 255, 0.2) 100%);
  border-radius: 50px;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 1px;
  color: #00ff88;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 14px;
}

.card-icon {
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.15) 0%, rgba(0, 212, 255, 0.15) 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  flex-shrink: 0;
}

.card-title {
  flex: 1;
  font-size: 18px;
  font-weight: 700;
  margin: 0;
}

.freq-badge,
.tech-badge,
.env-badge {
  padding: 4px 12px;
  background: rgba(0, 212, 255, 0.1);
  border-radius: 50px;
  font-size: 11px;
  color: #00d4ff;
  font-family: 'Courier New', monospace;
  font-weight: 600;
}

.tech-badge {
  background: rgba(255, 165, 0, 0.1);
  color: #ffa500;
}

.env-badge {
  background: rgba(0, 255, 136, 0.1);
  color: #00ff88;
}

.card-description {
  font-size: 14px;
  color: #9ca3af;
  margin: 0 0 18px 0;
  line-height: 1.6;
}

.card-details {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding: 12px 14px;
  background: rgba(31, 41, 55, 0.5);
  border-radius: 10px;
}

.detail-item.challenge {
  background: rgba(255, 107, 107, 0.08);
  border-left: 3px solid rgba(255, 107, 107, 0.3);
}

.detail-item.solution {
  background: rgba(0, 255, 136, 0.08);
  border-left: 3px solid rgba(0, 255, 136, 0.3);
}

.detail-item.highlight {
  background: linear-gradient(135deg, rgba(0, 255, 136, 0.1) 0%, rgba(0, 212, 255, 0.1) 100%);
  border-left: 3px solid rgba(0, 255, 136, 0.3);
}

.detail-label {
  font-size: 11px;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.detail-value {
  font-size: 14px;
  color: #d1d5db;
  line-height: 1.5;
}

.pros-cons {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.pros,
.cons {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  padding: 10px 12px;
  border-radius: 8px;
}

.pros {
  background: rgba(0, 255, 136, 0.08);
}

.cons {
  background: rgba(255, 165, 0, 0.08);
}

.pros-icon,
.cons-icon {
  font-size: 16px;
  margin-top: 1px;
}

.pros-text,
.cons-text {
  font-size: 13px;
  color: #d1d5db;
}
</style>