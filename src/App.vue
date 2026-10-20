<template>
  <div class="dashboard">
    <!-- 引入刚刚写好的 3D 组件 -->
    <ThreeScene />
    
    <!-- 盖在上面的 HTML UI 层 -->
    <div class="ui-layer">
      <!-- 顶部标题 -->
      <header class="header">
        <h1>全球数据实时监控中心</h1>
        <div class="time">{{ currentTime }}</div>
      </header>

      <!-- 左侧面板 -->
      <div class="panel left-panel">
        <div class="card">
          <h3>当前访问量</h3>
          <div class="number">1,245,890</div>
        </div>
        <div class="card">
          <h3>服务器状态</h3>
          <div class="number success">正常运行 (99.9%)</div>
        </div>
      </div>

      <!-- 右侧面板 -->
      <div class="panel right-panel">
        <div class="card">
          <h3>今日交易额 (USD)</h3>
          <div class="number warning">$ 45,230.00</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import ThreeScene from './components/ThreeScene.vue'

// 顶部时间
const currentTime = ref('')
let timer

const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleString()
}

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>

<style>
/* 全局背景色 */
body {
  background-color: #050a15;
  color: #fff;
  font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
}

.dashboard {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

/* UI 层，绝对定位盖在 3D 上面 */
.ui-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  pointer-events: none; /* 关键：让鼠标穿透 UI，能拖动后面的 3D 场景 */
}

/* 顶部标题栏 */
.header {
  position: absolute;
  top: 0;
  width: 100%;
  height: 80px;
  background: url('https://img.alicdn.com/tfs/TB1.8N.QXXXXXc.XFXXXXXXXXXX-1920-80.png') center top no-repeat;
  background-size: cover;
  display: flex;
  justify-content: center;
  align-items: center;
}

.header h1 {
  font-size: 28px;
  letter-spacing: 4px;
  color: #00eaff;
  text-shadow: 0 0 10px rgba(0, 234, 255, 0.8);
}

.header .time {
  position: absolute;
  right: 30px;
  top: 30px;
  color: #00eaff;
  font-size: 16px;
}

/* 左右面板 */
.panel {
  position: absolute;
  top: 120px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  pointer-events: auto; /* 卡片区域可以点击 */
}

.left-panel { left: 30px; }
.right-panel { right: 30px; }

/* 卡片样式（科技感边框） */
.card {
  width: 250px;
  padding: 20px;
  background: rgba(0, 30, 60, 0.6);
  border: 1px solid #0077ff;
  box-shadow: inset 0 0 20px rgba(0, 119, 255, 0.3);
  border-radius: 8px;
  backdrop-filter: blur(5px);
}

.card h3 {
  font-size: 14px;
  color: #88aaff;
  margin-bottom: 10px;
}

.card .number {
  font-size: 24px;
  font-weight: bold;
  color: #fff;
}
.card .success { color: #00ff88; }
.card .warning { color: #ffaa00; }
</style>