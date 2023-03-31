<template>
  <div ref="mount" class="model-container"></div>
</template>

<script setup>
import * as THREE from 'three'
import { onMounted, ref } from 'vue'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const mount = ref(null)
let scene, camera, renderer, orbitControls

onMounted(async () => {
  // 创建场景
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0xcfcfcf)

  // 创建相机
  const fov = 75
  const aspect = window.innerWidth / window.innerHeight
  const near = 0.1
  const far = 2000
  camera = new THREE.PerspectiveCamera(fov, aspect, near, far)
  camera.position.set(0, 100, 150)
  camera.lookAt(0, 0, 0)

  // 灯光
  const ambient = new THREE.AmbientLight(0x404040)
  scene.add(ambient)
  let pointLight = new THREE.PointLight(0xffffff, 1, 0)
  pointLight.position.set(20, 20, 20) //设置点光源位置
  pointLight.castShadow = true
  pointLight.shadow.mapSize.width = 1024 // 调整阴影贴图的宽度和高度以提高阴影质量
  pointLight.shadow.mapSize.height = 1024
  pointLight.shadow.camera.near = 1 // 调整光源摄像机的近裁剪面和远裁剪面以优化阴影范围
  pointLight.shadow.camera.far = 50

  scene.add(pointLight) //将点光源添加至场景

  // 创建渲染器
  renderer = new THREE.WebGLRenderer({ antialias: true })
  renderer.outputEncoding = THREE.sRGBEncoding
  renderer.physicallyCorrectLights = true
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setPixelRatio(window.devicePixelRatio)
  renderer.shadowMap.enabled = true

  // 鼠标拖拽
  orbitControls = new OrbitControls(camera, renderer.domElement)
  orbitControls.enableDamping = true
  orbitControls.rotateSpeed = 0.5
  orbitControls.zoomSpeed = 1.2
  orbitControls.panSpeed = 0.8
  orbitControls.minDistance = 0
  orbitControls.maxDistance = 9999
  console.log('orbitControls', orbitControls)

  mount.value.appendChild(renderer.domElement)

  // 加载模型
  const loader = new GLTFLoader()
  loader.load('/monitor.glb', (gltf) => {
    console.log('gltf.scene', gltf.scene)
    let model = gltf.scene
    scene.add(model)
    model.traverse(function (gltf) {
      if (gltf.isMesh) {
        // gltf.material.color.setHex(Math.random * 0xffffff)
        gltf.castShadow = true
        gltf.receiveShadow = true

        console.log('gltf', gltf)
      }
    })
    //设置整体场景的比例
    model.scale.set(5, 5, 5)
    function animate() {
      requestAnimationFrame(animate)
      orbitControls.update()
      renderer.render(scene, camera)
    }
    animate()
  })

  window.addEventListener('resize', onWindowResize, false)
})

function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.render(scene, camera)
  orbitControls.update()
}
</script>

<style scoped>
.model-container {
  width: 100%;
  height: 100%;
}
</style>
