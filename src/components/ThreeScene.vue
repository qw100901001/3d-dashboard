<template>
  <div ref="canvasContainer" class="three-container"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

const canvasContainer = ref(null)
let scene, camera, renderer, controls, animationId
let bars = []

onMounted(() => {
  initThree()
  animate()
  window.addEventListener('resize', onWindowResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', onWindowResize)
  cancelAnimationFrame(animationId)
  renderer.dispose()
})

const initThree = () => {
  // 1. 初始化场景
  scene = new THREE.Scene()
  // 加一点雾效，远处的柱子会变暗，更有空间感
  scene.fog = new THREE.FogExp2(0x050a15, 0.02)

  // 2. 初始化相机 (透视相机)
  camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000)
  camera.position.set(15, 15, 20)

  // 3. 初始化渲染器
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setPixelRatio(window.devicePixelRatio) // 适配高清屏
  canvasContainer.value.appendChild(renderer.domElement)

  // 4. 鼠标控制器 (允许拖拽旋转，滚轮缩放)
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
  controls.maxPolarAngle = Math.PI / 2 - 0.05 // 防止相机钻到地下

  // 5. 灯光 (配合物理材质显得高级)
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.4)
  scene.add(ambientLight)

  const pointLight = new THREE.PointLight(0x00aaff, 200, 100)
  pointLight.position.set(0, 20, 0)
  scene.add(pointLight)

  // 6. 底部网格 (科技感必备)
  const gridHelper = new THREE.GridHelper(50, 50, 0x0088ff, 0x002244)
  gridHelper.position.y = -0.01
  scene.add(gridHelper)

  // 7. 纯代码生成 3D 柱状图
  createBars()
}

const createBars = () => {
  // 模拟 20 条数据
  const dataCount = 20
  const spacing = 2 // 柱子间距
  
  for (let i = 0; i < dataCount; i++) {
    // 随机高度
    const height = Math.random() * 8 + 2 
    
    // 几何体：宽 1，高随数据，深 1
    const geometry = new THREE.BoxGeometry(1, height, 1)
    // 材质：玻璃/科技感
    const material = new THREE.MeshPhysicalMaterial({
      color: 0x00aaff,
      emissive: 0x0044ff, // 自发光
      transparent: true,
      opacity: 0.85,
      roughness: 0.1, // 表面光滑
      metalness: 0.8, // 金属感
      transmission: 0.5 // 玻璃质感
    })
    
    const bar = new THREE.Mesh(geometry, material)
    
    // 计算位置：排成一排或者矩阵
    bar.position.x = (i % 10) * spacing - 10
    bar.position.z = Math.floor(i / 10) * spacing - 4
    bar.position.y = height / 2 // 因为默认中心在几何体中间，所以Y轴要抬高一半，让它贴地
    
    // 记录初始高度，用于动画
    bar.userData = { baseHeight: height }
    
    scene.add(bar)
    bars.push(bar)
  }
}

// 窗口大小改变时自适应
const onWindowResize = () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
}

// 渲染循环
const animate = () => {
  animationId = requestAnimationFrame(animate)

  // 让柱子有呼吸跳动感
  const time = Date.now() * 0.002
  bars.forEach((bar, index) => {
    // 使用正弦函数让高度平滑变化
    const offset = Math.sin(time + index) * 0.5 + 1
    const newHeight = bar.userData.baseHeight * offset
    
    // 调整缩放 (改变高度)
    bar.scale.y = offset
    // 调整Y轴位置，保证底部一直贴着地面
    bar.position.y = newHeight / 2
  })

  controls.update()
  renderer.render(scene, camera)
}
</script>

<style scoped>
.three-container {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1; /* 放在底层 */
}
</style>