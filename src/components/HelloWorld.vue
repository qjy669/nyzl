<template>
  <div class="app-container">
    <!-- 头部 -->
    <header class="app-header">
      <h1 class="app-title">农银智链</h1>
      <div class="header-actions">
        <button class="icon-button">
          <Search class="icon" />
        </button>
      </div>
    </header>

    <!-- 主要内容 -->
    <main class="main-content">
      <!-- 首页 -->
      <div v-if="activeTab === 'home'" class="tab-content">
        <!-- 横幅 -->
        <div class="banner">
          <div class="banner-content">
            <h2 class="banner-title">智慧农业新时代</h2>
            <p class="banner-subtitle">数字化管理 · 科学种养 · 高效生产</p>
          </div>
        </div>

        <!-- 功能卡片 -->
        <div class="feature-grid">
          <div 
            v-for="(feature, index) in features" 
            :key="index" 
            class="feature-card"
            @click="activeTab = feature.tab"
          >
            <div class="feature-header">
              <component :is="feature.icon" class="feature-icon" :class="feature.iconClass" />
              <ChevronRight class="chevron-icon" />
            </div>
            <h3 class="feature-title">{{ feature.title }}</h3>
            <p class="feature-description">{{ feature.description }}</p>
          </div>
        </div>

        <!-- 数字孪生特性 -->
        <div class="digital-twin-card">
          <div class="card-header">
            <div class="header-with-icon">
              <BarChart2 class="icon" />
              <h3 class="card-title">数字孪生沙盘</h3>
            </div>
            <p class="card-subtitle">开启稻渔共生可视化管理新时代</p>
          </div>
          <div class="digital-twin-preview">
            <Activity class="preview-icon" />
            <span class="preview-text">点击查看实时数据</span>
          </div>
          <button class="primary-button white-button" @click="enterDigitalTwin">
            进入沙盘
          </button>
        </div>

        <!-- 最近活动 -->
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">最近活动</h3>
          </div>
          <div class="card-content">
            <div class="activity-list">
              <div 
                v-for="(activity, index) in activities" 
                :key="index"
                class="activity-item"
              >
                <div class="activity-icon-container">
                  <component :is="activity.icon" class="activity-icon" :class="activity.iconClass" />
                </div>
                <div class="activity-details">
                  <p class="activity-title">{{ activity.title }}</p>
                  <p class="activity-time">{{ activity.time }}</p>
                </div>
                <ChevronRight class="chevron-icon small" />
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 数字化管理标签页 -->
      <div v-if="activeTab === 'digitalManagement'" class="tab-content">
        <h2 class="page-title green">数字化信息管理</h2>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">生产数据概览</h3>
          </div>
          <div class="card-content">
            <div class="stats-grid">
              <div class="stat-card blue">
                <p class="stat-label">水稻种植面积</p>
                <p class="stat-value blue">128.5 亩</p>
              </div>
              <div class="stat-card green">
                <p class="stat-label">鱼塘面积</p>
                <p class="stat-value green">42.8 亩</p>
              </div>
              <div class="stat-card amber">
                <p class="stat-label">预计产量</p>
                <p class="stat-value amber">86.2 吨</p>
              </div>
              <div class="stat-card purple">
                <p class="stat-label">生长周期</p>
                <p class="stat-value purple">75 天</p>
              </div>
            </div>
          </div>
        </div>
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">记录管理</h3>
          </div>
          <div class="card-content">
            <div class="record-list">
              <button 
                v-for="(record, index) in records" 
                :key="index"
                class="record-button"
                :class="[record.buttonClass, selectedRecordIndex === index ? 'active' : '']"
                @click="handleRecordClick(index)"
              >
                <div class="button-with-icon">
                  <i class="fa fa-file-text-o icon"></i>
                  <span>{{ record.title }}</span>
                </div>
                <i class="fa fa-chevron-right chevron-icon"></i>
              </button>
            </div>

            <div class="record-content">
              <!-- 种植记录页面 -->
              <div v-if="selectedRecordIndex === 0">
                <h4>种植记录</h4>
                <p>水稻种植面积：128.5 亩</p>
                <p>生长周期：75 天</p>
                <p>预计产量：86.2 吨</p>
                <!-- 模拟功能：输入种植备注 -->
                <textarea v-model="plantingNote" rows="4" placeholder="写入种植备注"></textarea>
                <button @click="savePlantingNote">保存备注</button>
                <p>备注：{{ plantingNoteSaved }}</p>
              </div>

              <!-- 养殖记录页面 -->
              <div v-if="selectedRecordIndex === 1">
                <h4>养殖记录</h4>
                <p>鱼塘面积：42.8 亩</p>
                <p>生长周期：75 天</p>
                <p>预计产量：86.2 吨</p>
                <!-- 模拟功能：记录饲料用量 -->
                <input type="number" v-model.number="feedAmount" placeholder="输入饲料用量（kg）" />
                <button @click="saveFeedAmount">保存饲料数量</button>
                <p>已保存饲料用量：{{ feedAmountSaved }} kg</p>
              </div>

              <!-- 投入品记录页面 -->
              <div v-if="selectedRecordIndex === 2">
                <h4>投入品记录</h4>
                <p>记录肥料、种子、饲料等投入管理</p>
                <input v-model="inputItem" placeholder="输入投入品名称" />
                <input type="number" v-model.number="inputQuantity" placeholder="输入数量" />
                <button @click="addInputItem">添加投入品</button>

                <ul>
                  <li v-for="(item, idx) in inputItems" :key="idx">
                    {{ item.name }} — 数量：{{ item.quantity }}
                    <button @click="removeInputItem(idx)">删除</button>
                  </li>
                </ul>
              </div>

              <!-- 收获记录页面 -->
              <div v-if="selectedRecordIndex === 3">
                <h4>收获记录</h4>
                <p>记录收获重量及日期</p>
                <input type="number" v-model.number="harvestWeight" placeholder="输入收获重量（吨）" />
                <input type="date" v-model="harvestDate" />
                <button @click="saveHarvest">保存收获记录</button>

                <ul>
                  <li v-for="(harvest, idx) in harvestRecords" :key="idx">
                    {{ harvest.date }} – 重量：{{ harvest.weight }} 吨
                    <button @click="removeHarvestRecord(idx)">删除</button>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 病虫害识别标签页 -->
      <div v-if="activeTab === 'pestIdentification'" class="tab-content">
        <h2 class="page-title green">病虫害识别诊断系统</h2>

        <div class="pest-id-card">
          <div class="pest-id-header">
            <Camera class="pest-id-icon" />
            <h3 class="pest-id-title">拍照识别病虫害</h3>
            <p class="pest-id-subtitle">AI智能分析，秒出诊断结果</p>
          </div>
          <div class="upload-area">
            <p class="upload-text">点击上传或拍摄照片</p>
            <button class="primary-button white-button" @click="takePhoto">
              <Camera class="button-icon small" />
              拍照识别
            </button>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">最近识别记录</h3>
          </div>
          <div class="card-content">
            <div class="pest-record-list">
              <div class="pest-record red">
                <div class="pest-image"></div>
                <div class="pest-details">
                  <p class="pest-name red">稻瘟病</p>
                  <p class="pest-time">识别时间: 今天 14:30</p>
                  <p class="pest-confidence">置信度: 92%</p>
                </div>
                <button class="outline-button">查看详情</button>
              </div>

              <div class="pest-record amber">
                <div class="pest-image"></div>
                <div class="pest-details">
                  <p class="pest-name amber">稻飞虱</p>
                  <p class="pest-time">识别时间: 昨天 09:15</p>
                  <p class="pest-confidence">置信度: 87%</p>
                </div>
                <button class="outline-button">查看详情</button>
              </div>
            </div>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">常见病虫害</h3>
          </div>
          <div class="card-content">
            <div class="pest-grid">
              <div v-for="(pest, index) in commonPests" :key="index" class="pest-grid-item">
                <div class="pest-thumbnail"></div>
                <p class="pest-grid-name">{{ pest }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 验收机制标签页 -->
      <div v-if="activeTab === 'verification'" class="tab-content">
        <h2 class="page-title green">高效的验收机制</h2>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">待验收任务</h3>
          </div>
          <div class="card-content">
            <div class="task-list">
              <div v-for="(task, index) in verificationTasks" :key="index" class="task-item" :class="task.itemClass">
                <div class="task-header">
                  <h3 class="task-title">{{ task.title }}</h3>
                  <span class="status-badge">待验收</span>
                </div>
                <p class="task-date">计划验收时间: {{ task.date }}</p>
                <div class="task-actions">
                  <button class="outline-button">查看详情</button>
                  <button class="primary-button" :class="task.buttonClass">开始验收</button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">验收流程</h3>
          </div>
          <div class="card-content">
            <div class="timeline">
              <div v-for="(step, index) in verificationSteps" :key="index" class="timeline-item">
                <div class="timeline-marker" :class="step.markerClass">
                  <span class="timeline-number">{{ index + 1 }}</span>
                </div>
                <div class="timeline-content">
                  <h3 class="timeline-title">{{ step.title }}</h3>
                  <p class="timeline-description">{{ step.description }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- AI助手标签页 -->
      <div v-if="activeTab === 'aiAssistant'" class="tab-content">
        <h2 class="page-title green">农民院士智能体</h2>

        <div class="ai-assistant-card">
          <div class="ai-header">
            <div class="ai-avatar">
              <Zap class="ai-avatar-icon" />
            </div>
            <div class="ai-info">
              <h3 class="ai-title">农民院士</h3>
              <p class="ai-subtitle">您的专业农业顾问</p>
            </div>
          </div>
          <p class="ai-description">
            我是您的智能农业助手，可以回答种植养殖问题，提供专业建议，帮助您提高产量和质量。
          </p>
          <div class="consultation">
            <button v-if="!chatActive" class="primary-button" @click="startConsult">开始咨询</button>
            <div v-if="chatActive" class="chat-window">
              <div class="chat-messages" ref="chatMessages">
                <div
                  v-for="(msg, index) in messages"
                  :key="index"
                  :class="['chat-message', msg.isUser ? 'user' : 'ai']"
                >
                  <div class="message-content">{{ msg.text }}</div>
                </div>
              </div>

              <form @submit.prevent="sendMessage" class="input-area">
                <input
                  v-model="inputText"
                  type="text"
                  placeholder="请输入您的问题..."
                  autocomplete="off"
                  required
                  class="text-input"
                />
                <button type="submit" class="send-button" :disabled="sending">
                  {{ sending ? '发送中...' : '发送' }}
                </button>
              </form>

              <button class="cancel-button" @click="endConsult">结束咨询</button>
            </div>
          </div>

        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">常见问题</h3>
          </div>
          <div class="card-content">
            <div class="faq-list">
              <button 
                v-for="(question, index) in commonQuestions" 
                :key="index"
                class="faq-button"
              >
                <span>{{ question }}</span>
                <ChevronRight class="chevron-icon" />
              </button>
            </div>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">最近咨询记录</h3>
          </div>
          <div class="card-content">
            <div class="consultation-list">
              <div v-for="(record, index) in consultationRecords" :key="index" class="consultation-item">
                <p class="consultation-question">{{ record.question }}</p>
                <p class="consultation-time">{{ record.time }}</p>
                <button class="outline-button full-width">查看回答</button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 数字孪生标签页 -->
      <div v-if="activeTab === 'digitalTwin'" class="tab-content">
        <h2 class="page-title green">数字孪生沙盘</h2>

        <div class="digital-twin-banner">
          <h3 class="digital-twin-banner-title">稻渔共生数字孪生系统</h3>
          <p class="digital-twin-banner-subtitle">实时监控、数据分析、智能预警</p>

          <div class="digital-twin-preview large">
            <BarChart2 class="preview-icon large" />
            <p class="preview-text">3D可视化沙盘</p>
            <!-- 将点击事件改为打开沙盘 -->
            <button class="primary-button white-button small" @click="openSandbox">
              加载沙盘
            </button>
          </div>
        </div>

        <!-- 弹窗遮罩 -->
        <div
          v-if="visible"
          class="sandbox-overlay"
          @click.self="closeSandbox"
        >
          <div class="sandbox-container">
            <button class="close-btn" @click="closeSandbox">✕</button>
            <div ref="sandboxCanvas" class="sandbox-canvas"></div>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">实时监测数据</h3>
          </div>
          <div class="card-content">
            <div class="metrics-grid">
              <div v-for="(metric, index) in monitoringMetrics" :key="index" class="metric-card" :class="metric.cardClass">
                <div class="metric-header">
                  <p class="metric-name">{{ metric.name }}</p>
                  <span class="metric-status" :class="metric.statusClass">{{ metric.status }}</span>
                </div>
                <p class="metric-value" :class="metric.valueClass">{{ metric.value }}</p>
                <div class="progress-bar">
                  <div class="progress-fill" :class="metric.barClass" :style="{ width: metric.percentage + '%' }"></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="card">
          <div class="card-header">
            <h3 class="card-title">区域管理</h3>
          </div>
          <div class="card-content">
            <div class="area-list">
              <div v-for="(area, index) in areas" :key="index" class="area-item" :class="area.itemClass">
                <div class="area-header">
                  <h3 class="area-title">{{ area.name }}</h3>
                  <span class="area-status" :class="area.statusClass">{{ area.status }}</span>
                </div>
                <div class="area-info">
                  <span>{{ area.areaInfo }}</span>
                  <span>{{ area.additionalInfo }}</span>
                </div>
                <button class="outline-button full-width">查看详情</button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!--我的标签页-->
      <div v-if="activeTab === 'profile'" class="tab-content profile-page">
        <template v-if="!isLoggedIn">
          <h2>用户登录 / 注册</h2>

          <!-- 登录表单 -->
          <form @submit.prevent="handleLogin" v-if="!isRegister">
            <div class="form-group">
              <label for="loginUsername">用户名</label>
              <input id="loginUsername" v-model="loginForm.username" type="text" placeholder="请输入用户名" required />
            </div>
            <div class="form-group">
              <label for="loginPhone">手机号</label>
              <input id="loginPhone" v-model="loginForm.phone" type="tel" placeholder="请输入手机号" required />
            </div>
            <div class="form-group">
              <label for="loginPassword">密码</label>
              <input id="loginPassword" v-model="loginForm.password" type="password" placeholder="请输入密码" required />
            </div>
            <button type="submit" class="primary-button">登录</button>
            <p>
              还没有账号？
              <a href="#" @click.prevent="isRegister = true">去注册</a>
            </p>
          </form>

          <!-- 注册表单 -->
          <form @submit.prevent="handleRegister" v-if="isRegister">
            <div class="form-group">
              <label for="regUsername">用户名</label>
              <input id="regUsername" v-model="registerForm.username" type="text" placeholder="请输入用户名" required />
            </div>
            <div class="form-group">
              <label for="regPhone">手机号</label>
              <input id="regPhone" v-model="registerForm.phone" type="tel" placeholder="请输入手机号" required />
            </div>
            <div class="form-group">
              <label for="regPassword">密码</label>
              <input id="regPassword" v-model="registerForm.password" type="password" placeholder="请输入密码" required />
            </div>
            <div class="form-group">
              <label for="regConfirmPassword">确认密码</label>
              <input id="regConfirmPassword" v-model="registerForm.confirmPassword" type="password" placeholder="请确认密码" required />
            </div>
            <button type="submit" class="primary-button">注册</button>
            <p>
              已有账号？
              <a href="#" @click.prevent="isRegister = false">去登录</a>
            </p>
          </form>
        </template>

        <template v-else>
          <!-- 登录成功后显示的个人信息和介绍 -->
          <h2>欢迎，{{ user.username }}</h2>
          <button class="primary-button logout-button" @click="handleLogout">退出登录</button>

          <section class="introduction">
            <h3>农银智链微信小程序介绍</h3>
            <p>
              农银智链是一款自主开发的微信小程序，通过AI技术的加持，
              提供数字化信息管理和高效的验收机制。
              小程序融入病虫害智能识别功能，以及基于文心一言大模型的农民院士智能体，
              农业专家虚拟化身开启稻渔共生可视化管理新时代。
            </p>
            <p>
              我们致力于为农户提供全方位智能化服务，
              推动高附加值稻渔产品进入市场，助力农民增收致富。
            </p>
          </section>

          <section class="user-services">
          <h3>你的服务</h3>
          <button class="services-button" @click="activeTab = 'digitalManagement'">数字化信息管理</button>
          <button class="services-button" @click="activeTab = 'pestIdentification'">病虫害智能识别</button>
          <button class="services-button" @click="activeTab = 'aiAssistant'">农业专家虚拟化身咨询</button>
          <button class="services-button" @click="activeTab = 'verification'">高效质量验收</button>
        </section>
        </template>
      </div>
      
    </main>

    <!-- 底部导航 -->
    <div class="bottom-nav">
      <div 
        v-for="(tab, index) in navTabs" 
        :key="index"
        class="nav-item"
        :class="{ 'active': activeTab === tab.id }"
        @click="activeTab = tab.id"
      >
        <div class="nav-icon-container" :class="{ 'active-bg': activeTab === tab.id }">
          <component :is="tab.icon" class="nav-icon" />
        </div>
        <span class="nav-label">{{ tab.label }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onUnmounted, nextTick } from 'vue';
import * as THREE from 'three';

import { 
  Home, 
  Search, 
  Camera, 
  CheckSquare, 
  User, 
  ChevronRight, 
  BarChart2, 
  FileText, 
  AlertTriangle, 
  Zap, 
  Database, 
  Activity 
} from 'lucide-vue-next';

// 当前激活的标签页
const activeTab = ref('profile');


// 记录管理界面
const selectedRecordIndex = ref(0);
  const records = [
  { title: '种植记录', buttonClass: 'green-button' },
  { title: '养殖记录', buttonClass: 'blue-button' },
  { title: '投入品记录', buttonClass: 'amber-button' },
  { title: '收获记录', buttonClass: 'purple-button' }
];
// 种植记录备注
const plantingNote = ref('');
const plantingNoteSaved = ref('');
function savePlantingNote() {
  plantingNoteSaved.value = plantingNote.value;
  alert('种植备注已保存');
}
// 养殖记录饲料用量
const feedAmount = ref(null);
const feedAmountSaved = ref(null);
function saveFeedAmount() {
  feedAmountSaved.value = feedAmount.value;
  alert('饲料用量已保存');
}
// 投入品记录
const inputItem = ref('');
const inputQuantity = ref(null);
const inputItems = ref([]);
function addInputItem() {
  if (!inputItem.value || !inputQuantity.value) {
    alert('请输入完整信息');
    return;
  }
  inputItems.value.push({ name: inputItem.value, quantity: inputQuantity.value });
  inputItem.value = '';
  inputQuantity.value = null;
}
function removeInputItem(idx) {
  inputItems.value.splice(idx, 1);
}

// 收获记录
const harvestWeight = ref(null);
const harvestDate = ref('');
const harvestRecords = ref([]);
function saveHarvest() {
  if (!harvestWeight.value || !harvestDate.value) {
    alert('请输入收获重量和日期');
    return;
  }
  harvestRecords.value.push({ weight: harvestWeight.value, date: harvestDate.value });
  harvestWeight.value = null;
  harvestDate.value = '';
}

function removeHarvestRecord(idx) {
  harvestRecords.value.splice(idx, 1);
}

// 切换页面
function handleRecordClick(index) {
  selectedRecordIndex.value = index;
}

//ai聊天框
const chatActive = ref(false);
const inputText = ref("");
const messages = ref([]);
const sending = ref(false);

function startConsult() {
  chatActive.value = true;
  messages.value = [
    {
      text: "您好！我是您的农业专家智能助手，有什么我可以帮您的吗？",
      isUser: false,
    },
  ];
}

function endConsult() {
  chatActive.value = false;
  inputText.value = "";
  messages.value = [];
  sending.value = false;
}

function scrollToBottom() {
  nextTick(() => {
    const container = document.querySelector(".chat-messages");
    if (container) container.scrollTop = container.scrollHeight;
  });
}

function generateReply(question) {
  const q = question.toLowerCase();
  if (q.includes("病虫害识别") || q.includes("拍照识别") || q.includes("AI识别")) {
    return `您可以通过拍照上传作物叶片的清晰照片，AI智能助手将基于图像识别技术快速诊断病虫害类型。
系统支持多种主要农作物，识别准确率高达95%以上。
识别完成后，会提供针对性的防治建议和操作指导，帮助您科学管理病虫害。`;
  }
  if (q.includes("虫害") || q.includes("病害") || q.includes("害虫") || q.includes("防治") || q.includes("农药")) {
    return `针对病虫害问题，建议采取综合防治措施：
1. 及时施用绿色环保农药，避免过度使用化学农药导致抗药性;
2. 保持田间清洁，清理病残株和杂草，减少病源;
3. 结合定期巡查，及时发现和监测虫情发展;
4. 采用物理防治、诱捕和生物防治等方法，促进生态平衡。
科学合理的防治方案能够有效减少病虫害发生，提高作物健康和产量。`;
  }
  if (q.includes("收成") || q.includes("肥料") || q.includes("产量") || q.includes("施肥") || q.includes("灌溉")) {
    return `合理施肥和科学灌溉是提高作物产量的关键措施。
建议结合土壤检测数据和作物生长需求制定个性化施肥方案，精准施肥既能提升产量，又能保护环境。
灌溉方面，可采用节水灌溉技术，如滴灌和喷灌，保证土壤水分均衡，促进根系健康生长。
同时，注意关键生育期的水肥管理，配合生长调节剂使用，实现最佳生长效果。`;
  }
  if (q.includes("智能体") || q.includes("专家") || q.includes("助手") || q.includes("咨询") || q.includes("帮助")) {
    return `我是基于文心一言构建的农业专家智能体，整合了丰富的农艺知识和实践经验。
您可以向我咨询种植养殖中遇到的各种问题，包括病虫害防治、施肥灌溉、品种选择等。
我会结合最新的科研成果和实际案例，为您提供科学、有效的专业建议，助力您的农业生产更高效、更环保。`;
  }
  if (q.includes("数字孪生") || q.includes("沙盘") || q.includes("可视化") || q.includes("稻渔")) {
    return `数字孪生沙盘系统能够实时获取稻渔共生环境的多项参数，如水温、溶氧、PH值等，构建三维生态模型。
通过可视化界面，用户可以直观了解鱼群活动、稻田水质变化等信息。
系统还能自动分析风险，推荐养殖和管理策略，帮助您实现科学化、智能化的稻渔生产。`;
  }
  if (q.includes("验收") || q.includes("任务") || q.includes("流程")) {
    return `质量验收机制确保农产品符合国家和市场标准。
您可以在系统中查看待验收任务，系统提供详细的验收流程和标准指南，帮助您高效完成操作。
验收过程采用多重检测手段，包括农药残留、水分含量、品质指标等，确保产品安全和质量。`;
  }
 
  return `感谢您的提问！请您详细描述所遇到的问题，
包括具体作物类别、当前生长阶段、遇到的症状或困惑，以及所在地区环境特点。
这样我能为您提供更精准、更有针对性的解决方案，助力您的农业生产顺利进行。`;
}
async function sendMessage() {
  if (!inputText.value.trim() || sending.value) return;

  messages.value.push({ text: inputText.value.trim(), isUser: true });
  const userQuestion = inputText.value.trim();
  inputText.value = "";
  sending.value = true;

  scrollToBottom();

  // 模拟异步AI回复
  setTimeout(() => {
    const answer = generateReply(userQuestion);
    messages.value.push({ text: answer, isUser: false });
    sending.value = false;
    scrollToBottom();
  }, 1000);
}

// 登录注册
const isRegister = ref(false);
const isLoggedIn = ref(false);
const user = reactive({
  username: '',
  phone: ''
});

const loginForm = reactive({
  username: '',
  phone: '',
  password: ''
});

const registerForm = reactive({
  username: '',
  phone: '',
  password: '',
  confirmPassword: ''
});

// 模拟用户数据库
const userDatabase = ref([
  // 示例格式：{ username: 'user1', phone: '13800000000', password: '123456' }
]);

// 校验手机号格式
function isValidPhone(phone) {
  // 简单中国手机号校验示例
  return /^1[3-9]\d{9}$/.test(phone);
}

// 注册表单提交
function handleRegister() {
  const { username, phone, password, confirmPassword } = registerForm;

  if (!username || !phone || !password || !confirmPassword) {
    alert('请完整填写注册信息');
    return;
  }
  if (!isValidPhone(phone)) {
    alert('请输入有效的手机号');
    return;
  }
  if (password !== confirmPassword) {
    alert('两次密码输入不一致');
    return;
  }
  // 检查用户名唯一
  const exists = userDatabase.value.some(u => u.username === username);
  if (exists) {
    alert('用户名已存在，请更换其他用户名');
    return;
  }
  // 这里可以添加手机号是否已经注册的校验，若需要
  const phoneExists = userDatabase.value.some(u => u.phone === phone);
  if(phoneExists){
    alert('手机号已被注册，请直接登录');
    return;
  }

  // 注册成功，加入用户数据库
  userDatabase.value.push({ username, phone, password });

  alert('注册成功，请登录');
  isRegister.value = false;

  // 清空注册表单
  registerForm.username = '';
  registerForm.phone = '';
  registerForm.password = '';
  registerForm.confirmPassword = '';
}

// 登录表单提交
function handleLogin() {
  const { username, phone, password } = loginForm;

  if (!username || !phone || !password) {
    alert('请输入用户名、手机号和密码');
    return;
  }
  if (!isValidPhone(phone)) {
    alert('请输入有效的手机号');
    return;
  }

  // 验证用户名、手机号和密码是否匹配用户库
  const matchedUser = userDatabase.value.find(
    u => u.username === username && u.phone === phone && u.password === password
  );

  if (matchedUser) {
    user.username = matchedUser.username;
    user.phone = matchedUser.phone;
    isLoggedIn.value = true;

    alert('登录成功');

    // 清空登录表单
    loginForm.username = '';
    loginForm.phone = '';
    loginForm.password = '';
  } else {
    alert('用户名、手机号或密码错误');
  }
}

// 退出登录
function handleLogout() {
  isLoggedIn.value = false;

  user.username = '';
  user.phone = '';

  isRegister.value = false;

  loginForm.username = '';
  loginForm.phone = '';
  loginForm.password = '';
}


// 拍照识别功能
function takePhoto() {
  // 这里可以调用摄像头相关逻辑或者跳转到拍照页面
  console.log('拍照识别功能触发');
  // 例如跳转tab
  activeTab.value = 'pestIdentification';
  // 或者调用第三方接口打开摄像头等
}

const visible = ref(false);
const sandboxCanvas = ref(null);
let scene, camera, renderer, cube;
let animationId;

function enterDigitalTwin() {
  console.log('进入数字孪生沙盘');
  activeTab.value = 'digitalTwin';
}

function openSandbox() {
  visible.value = true;
  nextTick(() => {
    initThreeJS();
    animate();
  });
}

function closeSandbox() {
  visible.value = false;
  cancelAnimationFrame(animationId);
  
  if (renderer) {
    renderer.dispose();
    renderer = null;
  }
  
  if (sandboxCanvas.value) {
    sandboxCanvas.value.innerHTML = '';
  }

  window.removeEventListener('resize', handleResize);
}

function initThreeJS() {
  scene = new THREE.Scene();

  const width = sandboxCanvas.value.clientWidth;
  const height = sandboxCanvas.value.clientHeight;
  
  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.z = 5;

  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(width, height);
  
  sandboxCanvas.value.appendChild(renderer.domElement);

  const geometry = new THREE.BoxGeometry();
  const material = new THREE.MeshStandardMaterial({ color: 0x16a34a });
  cube = new THREE.Mesh(geometry, material);
  scene.add(cube);

  const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
  scene.add(ambientLight);

  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(5, 5, 5);
  scene.add(directionalLight);

  window.addEventListener('resize', handleResize, false);
}

function handleResize() {
  if (!sandboxCanvas.value || !renderer || !camera) return;
  
  const width = sandboxCanvas.value.clientWidth;
  const height = sandboxCanvas.value.clientHeight;

  camera.aspect = width / height;
  camera.updateProjectionMatrix();

  renderer.setSize(width, height);
}

function animate() {
  animationId = requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
}

onUnmounted(() => {
  cancelAnimationFrame(animationId);
  window.removeEventListener('resize', handleResize);
  if (renderer) {
    renderer.dispose();
    renderer = null;
  }
});

// async 方式加载沙盘数据示例
async function loadDigitalTwin() {
  console.log('加载沙盘数据中...');
  try {
    // 示例：模拟请求
    await new Promise(resolve => setTimeout(resolve, 1000));
    alert('沙盘数据加载完成');
  } catch (err) {
    console.error('加载沙盘数据失败:', err);
  }
}

// 功能卡片数据
const features = [
  {
    icon: Database,
    iconClass: 'green',
    title: '数字化信息管理',
    description: '全面记录和管理农业生产数据',
    tab: 'digitalManagement'
  },
  {
    icon: Camera,
    iconClass: 'red',
    title: '病虫害识别诊断',
    description: 'AI智能识别作物病虫害问题',
    tab: 'pestIdentification'
  },
  {
    icon: CheckSquare,
    iconClass: 'blue',
    title: '高效验收机制',
    description: '简化农产品质量验收流程',
    tab: 'verification'
  },
  {
    icon: User,
    iconClass: 'purple',
    title: '农民院士智能体',
    description: '专业农业知识咨询与指导',
    tab: 'aiAssistant'
  }
];

// 最近活动数据
const activities = [
  {
    icon: AlertTriangle,
    iconClass: 'amber',
    title: '水稻区域3号检测到可能的病害',
    time: '10分钟前'
  },
  {
    icon: CheckSquare,
    iconClass: 'green',
    title: '完成了东区鱼塘水质检测',
    time: '2小时���'
  },
  {
    icon: FileText,
    iconClass: 'blue',
    title: '更新了本周生产计划',
    time: '昨天'
  }
];

// 底部导航标签
const navTabs = [
  { id: 'home', icon: Home, label: '首页' },
  { id: 'digitalManagement', icon: Database, label: '数据' },
  { id: 'pestIdentification', icon: Camera, label: '识别' },
  { id: 'digitalTwin', icon: BarChart2, label: '沙盘' },
  { id: 'profile', icon: User, label: '我的' }
];


// 常见病虫害
const commonPests = ['稻瘟病', '稻飞虱', '纹枯病', '稻曲病', '鱼烂鳃病', '更多'];

// 验收任务
const verificationTasks = [
  { 
    title: '东区水稻收割验收', 
    date: '2025-03-28', 
    itemClass: 'green-task',
    buttonClass: 'green-bg'
  },
  { 
    title: '西区鱼塘捕捞验收', 
    date: '2025-03-30', 
    itemClass: 'blue-task',
    buttonClass: 'blue-bg'
  }
];

// 验收流程步骤
const verificationSteps = [
  { 
    title: '提交验收申请', 
    description: '填写验收信息，提交验收申请',
    markerClass: 'green-marker'
  },
  { 
    title: '现场验收', 
    description: '验收人员到场，进行现场检查和数据记录',
    markerClass: 'blue-marker'
  },
  { 
    title: '质量评估', 
    description: '对产品质量进行评估和分级',
    markerClass: 'purple-marker'
  },
  { 
    title: '验收确认', 
    description: '双方确认验收结果，完成验收流程',
    markerClass: 'amber-marker'
  }
];

// 常见问题
const commonQuestions = [
  '水稻种植最佳时间是什么时候？',
  '稻田养鱼如何控制水质？',
  '稻瘟病的早期症状有哪些？',
  '如何提高稻渔共生系统产量？'
];

// 咨询记录
const consultationRecords = [
  { 
    question: '如何防治水稻纹枯病？', 
    time: '2025-03-24 16:45'
  },
  { 
    question: '鱼塘水质浑浊怎么处理？', 
    time: '2025-03-22 09:30'
  }
];

// 监测指标
const monitoringMetrics = [
  {
    name: '水温',
    value: '26.5°C',
    status: '正常',
    percentage: 65,
    cardClass: 'blue-metric',
    statusClass: 'blue-status',
    valueClass: 'blue-value',
    barClass: 'blue-bar'
  },
  {
    name: '溶氧量',
    value: '7.2mg/L',
    status: '良好',
    percentage: 80,
    cardClass: 'green-metric',
    statusClass: 'green-status',
    valueClass: 'green-value',
    barClass: 'green-bar'
  },
  {
    name: 'pH值',
    value: '6.8',
    status: '注意',
    percentage: 60,
    cardClass: 'amber-metric',
    statusClass: 'amber-status',
    valueClass: 'amber-value',
    barClass: 'amber-bar'
  },
  {
    name: '氨氮',
    value: '0.5mg/L',
    status: '正常',
    percentage: 30,
    cardClass: 'purple-metric',
    statusClass: 'purple-status',
    valueClass: 'purple-value',
    barClass: 'purple-bar'
  }
];

// 区域管理
const areas = [
  {
    name: '东区稻田',
    status: '生长良好',
    areaInfo: '面积: 45亩',
    additionalInfo: '生长周期: 65天',
    itemClass: 'blue-area',
    statusClass: 'green-status'
  },
  {
    name: '西区鱼塘',
    status: '需要关注',
    areaInfo: '面积: 28亩',
    additionalInfo: '存鱼量: 2.5万尾',
    itemClass: 'green-area',
    statusClass: 'amber-status'
  }
];

</script>

<style scoped>
/* 基础样式 */
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background: linear-gradient(to bottom right, #f0f9ff, #f0fdf4);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

.icon {
  width: 20px;
  height: 20px;
}

.icon.small {
  width: 16px;
  height: 16px;
}
.button-icon.small {
  width: 16px;
  height: 16px;
  margin-right: 6px;
  vertical-align: middle;
}
/* 头部样式 */
.app-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
  background: linear-gradient(to right, #16a34a, #2563eb);
  color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.app-title {
  font-size: 20px;
  font-weight: bold;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.icon-button {
  background: transparent;
  border: none;
  color: white;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.icon-button:hover {
  background: rgba(255, 255, 255, 0.1);
}

/* 主要内容区域 */
.main-content {
  flex: 1;
  overflow: auto;
  padding: 16px;
}

.tab-content {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* 页面标题 */
.page-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 8px;
}

.page-title.green {
  color: #16a34a;
}

/* 卡片样式 */
.card{
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 16px;
}

.record-list {
  display: flex;
  gap: 10px;
  margin-bottom: 16px;
  flex-wrap: wrap;
}

.record-button {
  flex: 1;
  min-width: 120px;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  color: white;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: background-color 0.3s ease;
}

.record-button .button-with-icon {
  display: flex;
  align-items: center;
  gap: 8px;
}

.record-button .icon {
  font-size: 16px;
}

.record-button .chevron-icon {
  font-size: 14px;
}

.record-button.green-button { background-color: #4caf50; }
.record-button.blue-button { background-color: #2196f3; }
.record-button.amber-button { background-color: #ffb300; }
.record-button.purple-button { background-color: #9c27b0; }

.record-button.active {
  box-shadow: 0 0 8px #00000050;
  filter: brightness(1.1);
}

.record-content {
  border-top: 1px solid #eee;
  padding: 20px 16px;
  border-radius: 0 0 8px 8px;
  box-shadow: inset 0 0 10px #f0f0f0;
  min-height: 200px;
  font-family: "Microsoft Yahei", Arial, sans-serif;
}

/* 标题 h4 统一样式 */
.record-content h4 {
  margin-bottom: 12px;
  color: #333;
  font-weight: 600;
  border-left: 4px solid #5c9ded;
  padding-left: 8px;
  font-size: 1.3em;
}

/* 文字段落统一字体颜色和行间距 */
.record-content p {
  color: #555;
  line-height: 1.6em;
  margin-bottom: 10px;
}

/* 文本输入框和文本域 */
.record-content input[type="number"],
.record-content input[type="date"],
.record-content input[type="text"],
.record-content textarea,
.record-content input {
  width: 100%;
  max-width: 400px;
  padding: 8px 12px;
  margin: 8px 0 12px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1em;
  transition: border-color 0.25s ease;
  font-family: inherit;
  box-sizing: border-box;
}

.record-content textarea {
  resize: vertical;
}

/* 焦点高亮 */
.record-content input:focus,
.record-content textarea:focus {
  outline: none;
  border-color: #5c9ded;
  box-shadow: 0 0 5px #5c9ded66;
}

/* 按钮统一样式 */
.record-content button {
  cursor: pointer;
  background-color: #5c9ded;
  color: white;
  border: none;
  padding: 8px 18px;
  border-radius: 4px;
  font-size: 1em;
  transition: background-color 0.3s ease;
  margin-top: 4px;
  user-select: none;
}

.record-content button:hover {
  background-color: #2f7bde;
}

/* 列表样式 */
.record-content ul {
  margin-top: 14px;
  padding-left: 20px;
}

.record-content li {
  margin-bottom: 8px;
  color: #444;
  display: flex;
  align-items: center;
  justify-content: space-between;
  max-width: 420px;
  font-size: 1em;
  padding: 6px 12px;
  border-radius: 4px;
  box-shadow: 0 0 4px #eee;
}

/* 删除按钮样式 */
.record-content li button {
  padding: 2px 8px;
  font-size: 0.85em;
  background-color: #f44336;
  border: none;
  border-radius: 3px;
  color: white;
  cursor: pointer;
  user-select: none;
  transition: background-color 0.25s ease;
  margin-left: 12px;
}

.record-content li button:hover {
  background-color: #c62828;
}

/* 横幅 */
.banner {
  position: relative;
  height: 160px;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 24px;
}

.banner-content {
  position: absolute;
  inset: 0;
  background: linear-gradient(to right, rgba(22, 163, 74, 0.9), rgba(37, 99, 235, 0.9));
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  text-align: center;
  padding: 16px;
}

.banner-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 8px;
}

.banner-subtitle {
  font-size: 14px;
}

/* 功能卡片网格 */
.feature-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.feature-card {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.feature-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.feature-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.feature-icon {
  width: 32px;
  height: 32px;
}

.feature-icon.green {
  color: #16a34a;
}

.feature-icon.red {
  color: #dc2626;
}

.feature-icon.blue {
  color: #2563eb;
}

.feature-icon.purple {
  color: #9333ea;
}

.chevron-icon {
  width: 20px;
  height: 20px;
  color: #9ca3af;
}

.chevron-icon.small {
  width: 16px;
  height: 16px;
}

.feature-title {
  font-size: 16px;
  font-weight: bold;
  margin-top: 8px;
}

.feature-description {
  font-size: 14px;
  color: #6b7280;
  margin-top: 4px;
}

/* 数字孪生卡片 */
.digital-twin-card {
  background: linear-gradient(to right, #2563eb, #06b6d4);
  color: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  padding: 16px;
}

.header-with-icon {
  display: flex;
  align-items: center;
}

.digital-twin-preview {
  height: 128px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 16px 0;
}

.digital-twin-preview.large {
  height: 240px;
}

.preview-icon {
  width: 40px;
  height: 40px;
  margin-bottom: 8px;
}

.preview-icon.large {
  width: 48px;
  height: 48px;
}

.preview-text {
  font-size: 14px;
}

/* 按钮样式 */
.primary-button {
  display: block;
  width: 100%;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.2s;
}

.primary-button.white-button {
  background-color: white;
  color: #2563eb;
}

.primary-button.white-button:hover {
  background-color: #f0f9ff;
}

.primary-button.small {
  padding: 4px 12px;
  font-size: 14px;
}

.primary-button.green-bg {
  background-color: #16a34a;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
}

.sandbox-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.65);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.sandbox-container {
  position: relative;
  width: 90vw;
  height: 80vh;
  background: white;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 8px 20px rgba(0,0,0,0.3);
}

.sandbox-canvas {
  width: 100%;
  height: 100%;
  display: block;
}

.close-btn {
  position: absolute;
  top: 12px;
  right: 16px;
  background: transparent;
  border: none;
  font-size: 24px;
  cursor: pointer;
  font-weight: 700;
  color: #555;
}

.close-btn:hover {
  color: #16a34a;
}

/* 活动列表 */
.activity-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.activity-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 8px;
  border-radius: 8px;
  cursor: pointer;
}

.activity-item:hover {
  background-color: #f9fafb;
}

.activity-icon-container {
  background-color: #f3f4f6;
  padding: 8px;
  border-radius: 50%;
}

.activity-icon {
  width: 20px;
  height: 20px;
}

.activity-icon.amber {
  color: #d97706;
}

.activity-icon.green {
  color: #16a34a;
}

.activity-icon.blue {
  color: #2563eb;
}

.activity-details {
  flex: 1;
}

.activity-title {
  font-size: 14px;
  font-weight: 500;
}

.activity-time {
  font-size: 12px;
  color: #6b7280;
}

/* 底部导航 */
.bottom-nav {
  display: flex;
  justify-content: space-around;
  background-color: white;
  border-top: 1px solid #e5e7eb;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 8px 16px;
  color: #6b7280;
  cursor: pointer;
}

.nav-item.active {
  color: #16a34a;
}

.nav-icon-container {
  padding: 4px;
  border-radius: 50%;
}

.nav-icon-container.active-bg {
  background-color: #dcfce7;
}

.nav-icon {
  width: 20px;
  height: 20px;
}

.nav-label {
  font-size: 12px;
  margin-top: 4px;
}

/* 统计卡片 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.stat-card {
  padding: 16px;
  border-radius: 8px;
}

.stat-card.blue {
  background-color: #eff6ff;
}

.stat-card.green {
  background-color: #f0fdf4;
}

.stat-card.amber {
  background-color: #fffbeb;
}

.stat-card.purple {
  background-color: #faf5ff;
}

.stat-label {
  font-size: 14px;
  color: #6b7280;
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
  margin-top: 4px;
}

.stat-value.blue {
  color: #2563eb;
}

.stat-value.green {
  color: #16a34a;
}

.stat-value.amber {
  color: #d97706;
}

.stat-value.purple {
  color: #9333ea;
}

/* 记录按钮 */
.record-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.record-button {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  border-radius: 6px;
  border: none;
  font-weight: 500;
  cursor: pointer;
  text-align: left;
}

.button-with-icon {
  display: flex;
  align-items: center;
  gap: 8px;
}

.record-button.green-button {
  background-color: #f0fdf4;
  color: #16a34a;
}

.record-button.green-button:hover {
  background-color: #dcfce7;
}

.record-button.blue-button {
  background-color: #eff6ff;
  color: #2563eb;
}

.record-button.blue-button:hover {
  background-color: #dbeafe;
}

.record-button.amber-button {
  background-color: #fffbeb;
  color: #d97706;
}

.record-button.amber-button:hover {
  background-color: #fef3c7;
}

.record-button.purple-button {
  background-color: #faf5ff;
  color: #9333ea;
}

.record-button.purple-button:hover {
  background-color: #f3e8ff;
}

/* 病虫害识别卡片 */
.pest-id-card {
  background: linear-gradient(to right, #16a34a, #2563eb);
  color: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  padding: 24px 16px 16px;
}

.pest-id-header {
  text-align: center;
  margin-bottom: 16px;
}

.pest-id-icon {
  width: 48px;
  height: 48px;
  margin: 0 auto 8px;
}

.pest-id-title {
  font-size: 20px;
  font-weight: bold;
}

.pest-id-subtitle {
  font-size: 14px;
  color: #93c5fd;
}

.upload-area {
  height: 192px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  border: 2px dashed rgba(255, 255, 255, 0.4);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-bottom: 16px;
}

.upload-text {
  font-size: 14px;
  margin-bottom: 8px;
}

.button-icon {
  margin-right: 8px;
}

/* 病虫害记录 */
.pest-record-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.pest-record {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  border-radius: 8px;
}

.pest-record.red {
  background-color: #fef2f2;
}

.pest-record.amber {
  background-color: #fffbeb;
}

.pest-image {
  width: 64px;
  height: 64px;
  background-color: #e5e7eb;
  border-radius: 6px;
  flex-shrink: 0;
}

.pest-details {
  flex: 1;
}

.pest-name {
  font-weight: 500;
}

.pest-name.red {
  color: #dc2626;
}

.pest-name.amber {
  color: #d97706;
}

.pest-time, .pest-confidence {
  font-size: 12px;
  color: #6b7280;
}

/* 病虫害网格 */
.pest-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
}

.pest-grid-item {
  text-align: center;
}

.pest-thumbnail {
  height: 80px;
  background-color: #f3f4f6;
  border-radius: 6px;
  margin-bottom: 4px;
}

.pest-grid-name {
  font-size: 12px;
}

/* 验收任务 */
.task-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.task-item {
  padding: 12px;
  border-radius: 8px;
  border: 1px solid;
}

.task-item.green-task {
  border-color: #86efac;
  background-color: #f0fdf4;
}

.task-item.blue-task {
  border-color: #93c5fd;
  background-color: #eff6ff;
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.task-title {
  font-weight: 500;
}

.status-badge {
  font-size: 12px;
  background-color: #fef3c7;
  color: #92400e;
  padding: 4px 8px;
  border-radius: 9999px;
}

.task-date {
  font-size: 14px;
  color: #6b7280;
  margin-bottom: 8px;
}

.task-actions {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}

/* 时间线 */
.timeline {
  position: relative;
}

.timeline::before {
  content: '';
  position: absolute;
  left: 16px;
  top: 0;
  bottom: 0;
  width: 2px;
  background-color: #e5e7eb;
}

.timeline-item {
  position: relative;
  padding-left: 40px;
  padding-bottom: 24px;
}

.timeline-marker {
  position: absolute;
  left: 8px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.timeline-marker.green-marker {
  background-color: #16a34a;
}

.timeline-marker.blue-marker {
  background-color: #2563eb;
}

.timeline-marker.purple-marker {
  background-color: #9333ea;
}

.timeline-marker.amber-marker {
  background-color: #d97706;
}

.timeline-number {
  color: white;
  font-size: 12px;
}

.timeline-title {
  font-weight: 500;
  margin-bottom: 4px;
}

.timeline-description {
  font-size: 14px;
  color: #6b7280;
}

/* AI助手卡片 */
.ai-assistant-card {
  background: linear-gradient(to right, #9333ea, #2563eb);
  color: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  padding: 24px 16px;
}

.ai-header {
  display: flex;
  align-items: center;
  margin-bottom: 16px;
}

.ai-avatar {
  width: 64px;
  height: 64px;
  background-color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 16px;
}

.ai-avatar-icon {
  width: 32px;
  height: 32px;
  color: #9333ea;
}

.ai-title {
  font-size: 20px;
  font-weight: bold;
}

.ai-subtitle {
  font-size: 14px;
  color: #93c5fd;
}

.ai-description {
  font-size: 14px;
  margin-bottom: 16px;
}

/* FAQ按钮 */
.faq-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.faq-button {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background-color: #f9fafb;
  color: #4b5563;
  border: none;
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  text-align: left;
}

.faq-button:hover {
  background-color: #f3f4f6;
}

/* 咨询记录 */
.consultation-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.consultation-item {
  padding: 12px;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
}

.consultation-question {
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 4px;
}

.consultation-time {
  font-size: 12px;
  color: #6b7280;
  margin-bottom: 8px;
}

/* 数字孪生横幅 */
.digital-twin-banner {
  background: linear-gradient(to right, #2563eb, #06b6d4);
  color: white;
  border-radius: 8px;
  padding: 16px;
}

.digital-twin-banner-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 8px;
}

.digital-twin-banner-subtitle {
  font-size: 14px;
  margin-bottom: 16px;
}

/* 监测指标 */
.metrics-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.metric-card {
  padding: 12px;
  border-radius: 8px;
}

.metric-card.blue-metric {
  background-color: #eff6ff;
}

.metric-card.green-metric {
  background-color: #f0fdf4;
}

.metric-card.amber-metric {
  background-color: #fffbeb;
}

.metric-card.purple-metric {
  background-color: #faf5ff;
}

.metric-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.metric-name {
  font-size: 14px;
  font-weight: 500;
}

.metric-status {
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 9999px;
}

.metric-status.blue-status {
  background-color: #dbeafe;
  color: #1e40af;
}

.metric-status.green-status {
  background-color: #dcfce7;
  color: #166534;
}

.metric-status.amber-status {
  background-color: #fef3c7;
  color: #92400e;
}

.metric-status.purple-status {
  background-color: #f3e8ff;
  color: #6b21a8;
}

.metric-value {
  font-size: 24px;
  font-weight: bold;
}

.metric-value.blue-value {
  color: #2563eb;
}

.metric-value.green-value {
  color: #16a34a;
}

.metric-value.amber-value {
  color: #d97706;
}

.metric-value.purple-value {
  color: #9333ea;
}

.progress-bar {
  height: 8px;
  background-color: #e5e7eb;
  border-radius: 9999px;
  margin-top: 8px;
}

.progress-fill {
  height: 8px;
  border-radius: 9999px;
}

.progress-fill.blue-bar {
  background-color: #2563eb;
}

.progress-fill.green-bar {
  background-color: #16a34a;
}

.progress-fill.amber-bar {
  background-color: #d97706;
}

.progress-fill.purple-bar {
  background-color: #9333ea;
}

/* 区域管理 */
.area-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.area-item {
  padding: 12px;
  border-radius: 8px;
  border: 1px solid;
}

.area-item.blue-area {
  border-color: #93c5fd;
  background-color: #eff6ff;
}

.area-item.green-area {
  border-color: #86efac;
  background-color: #f0fdf4;
}

.area-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.area-title {
  font-weight: 500;
}

.area-status {
  font-size: 12px;
  padding: 4px 8px;
  border-radius: 9999px;
}

.area-status.green-status {
  background-color: #dcfce7;
  color: #166534;
}

.area-status.amber-status {
  background-color: #fef3c7;
  color: #92400e;
}

.area-info {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  color: #6b7280;
  margin-bottom: 8px;
}


/* ai助手聊天界面 */
.consultation {
  max-width: 480px;
  margin: 20px auto;
  padding: 12px 16px;
  background-color: #f9fafb;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgb(0 0 0 / 0.1);
  font-family: "PingFang SC", "Helvetica Neue", Helvetica, Arial, sans-serif;
  color: #333;
  display: flex;
  flex-direction: column;
}
/* 按钮整体风格统一 */
.primary-button {
  width: 100%;
  background-color: #16a34a;
  color: white;
  height: 40px;
  border: none;
  border-radius: 6px;
  font-weight: 700;
  font-size: 16px;
  cursor: pointer;
  margin-top: 10px;
  transition: background-color 0.3s ease;
  box-shadow: none;
  align-self: stretch;
  padding: 0;
  user-select: none;
}
.primary-button:hover:not(:disabled) {
  background-color: #15803d;
  box-shadow: none;
}
.primary-button:disabled {
  background-color: #bbf7d0;
  cursor: not-allowed;
  box-shadow: none;
}

/* 取消按钮调整 */
.cancel-button {
  align-self: center;
  margin: 18px 0 0 0;
  background: none;
  border: none;
  color: #16a34a;
  font-weight: 700;
  font-size: 15px;
  cursor: pointer;
  user-select: none;
  padding: 8px 20px;
  border-radius: 6px;
  transition: background-color 0.3s ease;
}
.cancel-button:hover {
  background-color: #dcffe5;
}

/* 聊天窗口背景和边框调整 */
.chat-window {
  width: 100%;
  max-height: 80vh;
  min-height: 320px;
  border: 1.5px solid #16a34a;
  border-radius: 10px;
  background-color: #ecfdf5;
  box-shadow: inset 0 0 12px rgb(22 163 74 / 0.15);
  display: flex;
  flex-direction: column;
}

/* 消息区域 */
.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 16px 20px;
  scrollbar-width: thin;
  scrollbar-color: #a4d89f transparent;
  display: flex;
  flex-direction: column;
  gap: 14px;
}
.chat-messages::-webkit-scrollbar {
  width: 8px;
}
.chat-messages::-webkit-scrollbar-thumb {
  background-color: #a4d89f;
  border-radius: 6px;
}

/* 消息样式 */
.chat-message {
  max-width: 72%;
  min-width: 120px;
  line-height: 1.5;
  padding: 12px 16px;
  border-radius: 12px;
  word-break: break-word;
  font-size: 15px;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
  user-select: text;
  opacity: 0.95;
  transition: background-color 0.25s ease;
}
/* 用户消息 */
.chat-message.user {
  align-self: flex-end;
  background-color: #16a34a;
  color: white;
  border-bottom-right-radius: 4px;
}
/* AI消息 */
.chat-message.ai {
  align-self: flex-start;
  background-color: #d4f7dd;
  color: #065f46;
  border-bottom-left-radius: 4px;
}

/* 输入区域 */
.input-area {
  display: flex;
  padding: 12px 16px;
  border-top: 1.5px solid #16a34a;
  background-color: #ecfdf5;
  gap: 12px;
  align-items: center;
  flex-wrap: nowrap;
}

/* 输入框 */
.text-input {
  flex-grow: 1;
  min-width: 0;
  border: 1px solid #d1d5db;
  border-radius: 6px;
  font-size: 14px;
  padding: 6px 10px;
  outline-offset: 2px;
  transition: border-color 0.3s ease;
  font-weight: 600;
  color: #333;
  background-color: #fff;
  font-family: "PingFang SC", "Helvetica Neue", Helvetica, Arial, sans-serif;
}
.text-input::placeholder {
  color: #a7c7a3;
  font-weight: 400;
}
.text-input:focus {
  border-color: #16a34a;
  background-color: #ffffff;
  box-shadow: 0 0 8px #34d399aa;
}

/* 发送按钮 */
.send-button {
  background-color: #16a34a;
  color: white;
  border: none;
  font-weight: 700;
  font-size: 15px;
  padding: 8px 20px;
  border-radius: 6px;
  cursor: pointer;
  user-select: none;
  box-shadow: none;
  white-space: nowrap;
  flex-shrink: 0;
  transition: background-color 0.3s ease;
}
.send-button:hover:not(:disabled) {
  background-color: #15803d;
}
.send-button:disabled {
  background-color: #bbf7d0;
  cursor: not-allowed;
  box-shadow: none;
}

/* 响应式调整 */
@media (max-width: 400px) {
  .consultation {
    width: 95%;
    margin: 20px auto;
    padding: 12px 16px;
  }

  .primary-button, .send-button {
    padding: 8px 16px;
    font-size: 14px;
  }

  .chat-message {
    max-width: 85%;
    font-size: 14px;
    padding: 10px 14px;
  }

  .input-area {
    padding: 10px 16px;
    gap: 10px;
  }

  .text-input {
    font-size: 14px;
    padding: 6px 10px;
  }
}

/* 登录注册 */
.profile-page {
  max-width: 480px;
  margin: 20px auto;
  padding: 12px 16px;
  background-color: #f9fafb;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgb(0 0 0 / 0.1);
  font-family: "PingFang SC", "Helvetica Neue", Helvetica, Arial, sans-serif;
  color: #333;
}

h2 {
  margin-bottom: 16px;
  font-weight: 700;
  color: #16a34a;
}

.form-group {
  margin-bottom: 14px;
}
.form-group label {
  display: block;
  margin-bottom: 6px;
  font-weight: 600;
}
.form-group input {
  width: 100%;
  height: 36px;
  padding: 6px 10px;
  border-radius: 6px;
  border: 1px solid #d1d5db;
  box-sizing: border-box;
  font-size: 14px;
}
.primary-button {
  width: 100%;
  background-color: #16a34a;
  color: white;
  height: 40px;
  border: none;
  border-radius: 6px;
  font-weight: 700;
  font-size: 16px;
  cursor: pointer;
  margin-top: 10
}
.primary-button:hover {
  background-color: #15803d;
}

p {
  margin-top: 12px;
  font-size: 14px;
}
p a {
  color: #16a34a;
  text-decoration: underline;
  cursor: pointer;
}

.logout-button {
  width: auto;
  margin-bottom: 24px;
  padding: 8px 20px;
}

.introduction, .user-services {
  background: #ecfdf5;
  border: 1px solid #16a34a;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 18px;
}
.introduction h3,
.user-services {
  background: #ecfdf5;
  border: 1px solid #16a34a;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 18px;
}

.user-services h3 {
  color: #065f46;
  margin-bottom: 12px;
  font-weight: 600;
  font-size: 20px;
}

.services-button {
  width: 200px;             
  padding: 10px 16px;      
  margin: 12px auto;      
  background-color: #30a85c;
  color: white;
  font-size: 15px;          
  font-weight: 550;        
  border: none;
  border-radius: 5px;      
  cursor: pointer;
  transition: background-color 0.3s ease;
  text-align: center;
  user-select: none;
  display: block;           
}

.services-button:hover {
  background-color: #138d3b;
}

.services-button:active {
  background-color: #0f6f2a;
}
</style>

