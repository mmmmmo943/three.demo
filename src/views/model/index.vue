<template>
  <div @click="changeColor" style="cursor: pointer; margin-bottom: 50px">透明</div>
  <div @click="changeColor2" style="cursor: pointer; margin-bottom: 50px">不透明</div>
  <div ref="mount" class="model-container"></div>
</template>

<script setup>
import * as THREE from 'three'
import { onMounted, ref } from 'vue'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import { ElMessage } from 'element-plus'

import { gsap } from 'gsap'

const scene = new THREE.Scene()
scene.background = new THREE.Color(0xcfcfcf)

// 副相机
const secondaryCamera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / 2 / window.innerHeight,
  0.1,
  1000
)
secondaryCamera.position.set(50, 50, 50)
secondaryCamera.lookAt(0, 0, 0)

// 创建副相机的实体（圆球）
const sphereGeometry = new THREE.SphereGeometry(5, 32, 32)
const sphereMaterial = new THREE.MeshBasicMaterial({ color: 0xffff00 })
const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)
sphere.position.copy(secondaryCamera.position)
scene.add(sphere)

// 主相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / 2 / window.innerHeight,
  0.1,
  1000
)
camera.position.set(0, 100, 0)
camera.lookAt(0, 0, 0)

// 创建主相机的实体（圆球）
const sphereGeometryMain = new THREE.SphereGeometry(0.5, 20, 20)
const sphereMaterialMain = new THREE.MeshBasicMaterial({ color: 0x0000ff }) // 使用蓝色来区分主相机
const sphereMain = new THREE.Mesh(sphereGeometryMain, sphereMaterialMain)
sphereMain.position.copy(camera.position)
scene.add(sphereMain)

const renderer = new THREE.WebGLRenderer()

renderer.setSize(window.innerWidth, window.innerHeight)

//光线
const ambient = new THREE.AmbientLight(0xffffff)
scene.add(ambient)
const dirLight = new THREE.DirectionalLight(0xffffff, 1)
dirLight.position.set(0, 0, 500) //设置点光源位置
dirLight.castShadow = true
dirLight.shadow.camera.visible = true
dirLight.shadow.mapSize.width = 1024 // 调整阴影贴图的宽度和高度以提高阴影质量
dirLight.shadow.mapSize.height = 1024
dirLight.shadow.camera.near = 0.5 // 调整光源摄像机的近裁剪面和远裁剪面以优化阴影范围
dirLight.shadow.camera.far = 100
scene.add(dirLight) //将点光源添加至场景

// 控制器
const orbitControls = new OrbitControls(camera, renderer.domElement)
const orbitControlsSecondary = new OrbitControls(secondaryCamera, renderer.domElement)

orbitControls.enableDamping = true
orbitControls.rotateSpeed = 0.5
orbitControls.zoomSpeed = 1.2
orbitControls.panSpeed = 0.8
orbitControls.minDistance = 0
orbitControls.maxDistance = 9999

orbitControlsSecondary.enableDamping = true
orbitControlsSecondary.rotateSpeed = 0.5
orbitControlsSecondary.zoomSpeed = 1.2
orbitControlsSecondary.panSpeed = 0.8
orbitControlsSecondary.minDistance = 0
orbitControlsSecondary.maxDistance = 9999

document.addEventListener('mousedown', (event) => {
  const canvasBounds = renderer.domElement.getBoundingClientRect()
  const mouseX = event.clientX - canvasBounds.left

  if (mouseX < canvasBounds.width / 2) {
    orbitControls.enabled = true
    orbitControlsSecondary.enabled = false
  } else {
    orbitControls.enabled = false
    orbitControlsSecondary.enabled = true
  }
})

const model_box = ref(null)

const raycaster = new THREE.Raycaster()
const mouse = new THREE.Vector2()
const objects = []

let targetPosition = new THREE.Vector3()
let current
let target
let tween

document.addEventListener('click', onDocumentClick, false)
function onDocumentClick(event) {
  event.preventDefault()
  const canvasBounds = mount.value.getBoundingClientRect()

  // 鼠标在画布中的位置
  const mouseX = event.clientX - canvasBounds.left
  const mouseY = event.clientY - canvasBounds.top

  // 检查鼠标点击的是哪个视口
  let viewport
  if (mouseX < canvasBounds.width / 2) {
    // 鼠标点击的是左边的视口
    viewport = {
      left: 0,
      width: canvasBounds.width / 2,
      camera: camera
    }
  } else {
    // 鼠标点击的是右边的视口
    viewport = {
      left: canvasBounds.width / 2,
      width: canvasBounds.width / 2,
      camera: secondaryCamera
    }
  }

  mouse.x = ((mouseX - viewport.left) / viewport.width) * 2 - 1
  mouse.y = -(mouseY / canvasBounds.height) * 2 + 1

  raycaster.setFromCamera(mouse, viewport.camera)
  const intersects = raycaster.intersectObjects(objects, true)
  if (intersects.length > 0) {
    // 在onMouseClick函数中，设置目标位置为点击的模型部件的位置

    // 将目标位置设置为点击的模型部件的位置
    targetPosition.set(intersects[0].point)

    const aabb = new THREE.Box3().setFromObject(intersects[0].object)
    const center = aabb.getCenter(new THREE.Vector3())
    const size = aabb.getSize(new THREE.Vector3())

    // 特写缓动函数
    gsap.to(camera.position, {
      duration: 2,
      x: center.x,
      y: center.y + size.y + 1, // 摄像机位置高于目标位置
      z: center.z + size.z + 1,
      onUpdate: function () {
        camera.lookAt(intersects[0].object.position)
      }
    })

    gsap.to(orbitControls.target, {
      duration: 2,
      x: center.x,
      y: center.y,
      z: center.z,
      onComplete: function () {
        orbitControls.enabled = true
      }
    })
  }
}

const loader2 = ref(new GLTFLoader())
let model

loader2.value.load('/model-revit-gltf.glb', (gltf) => {
  console.log('gltf.scene', gltf.scene)
  model = gltf.scene
  gltf.scene.position.set(0, 0, 0)
  model_box.value = gltf.scene
  objects.push(gltf.scene)
  scene.add(model)
  model.traverse(function (gltf) {
    if (gltf.name == 'monitor_switch') {
      console.log('gltf', gltf)
    }
  })
  //设置整体场景的比例
  model.scale.set(1, 1, 1)
  window.addEventListener('resize', onWindowResize, false)
})

let INTERSECTED

// 初始位置
const radius = 10 // 可以根据实际情况调整
let theta = 0 // 角度
const speed = 0.4 // 速度

function animate() {
  requestAnimationFrame(animate)

  const intersects = raycaster.intersectObjects(objects, true)
  if (intersects.length > 0) {
    if (INTERSECTED != intersects[0].object) {
      // 这里选中的物体是上一个选中物体。
      if (INTERSECTED) {
        // 把上一个选中的物体设置为当前色。
        INTERSECTED.material.emissive.setHex(INTERSECTED.currentHex)
      }
      // 设置当前选中的物体
      INTERSECTED = intersects[0].object
      // 保留当前选中物体，**原本的颜色**
      INTERSECTED.currentHex = INTERSECTED.material.emissive.getHex()
      // 设置当前选中的物体颜色为红色
      INTERSECTED.material.emissive.setHex(0x00ffff)
      // alert('构件名称:' + INTERSECTED.name)
      ElMessage.success('构件名称:' + INTERSECTED.name)
      console.log('INTERSECTED', INTERSECTED)
      console.log('INTERSECTED.name', INTERSECTED.name)
    }
    // 如果没有相交的物体，把选中的物体设置为原来的颜色
  } else {
    if (INTERSECTED) {
      INTERSECTED.material.emissive.setHex(INTERSECTED.currentHex)
    }
    // 清空选中物体
    INTERSECTED = null
  }

  orbitControls.update()
  orbitControlsSecondary.update()

  // 更新副相机的位置
  theta += speed // 速度
  sphere.position.x = radius * Math.sin(THREE.MathUtils.degToRad(theta))
  sphere.position.z = radius * Math.cos(THREE.MathUtils.degToRad(theta))
  secondaryCamera.position.copy(sphere.position)

  // 更新主相机的实体位置
  sphereMain.position.copy(camera.position)

  // 让相机看向中心点
  secondaryCamera.lookAt(new THREE.Vector3(0, 0, 0))

  // 渲染主相机的视口
  renderer.setScissorTest(true)
  renderer.setScissor(0, 0, window.innerWidth / 2, window.innerHeight)
  renderer.setViewport(0, 0, window.innerWidth / 2, window.innerHeight)
  renderer.render(scene, camera)

  // 渲染副相机的视口
  renderer.setScissor(window.innerWidth / 2, 0, window.innerWidth / 2, window.innerHeight)
  renderer.setViewport(window.innerWidth / 2, 0, window.innerWidth / 2, window.innerHeight)
  renderer.render(scene, secondaryCamera)

  renderer.setScissorTest(false)
}

animate()

const mount = ref(null)

onMounted(() => {
  mount.value.appendChild(renderer.domElement)
  onWindowResize()
  animate()
  // window.addEventListener('mousemove', onDocumentMouseMove)
})

const onWindowResize = () => {
  camera.aspect = mount.value.clientWidth / (2 * mount.value.clientHeight)
  secondaryCamera.aspect = mount.value.clientWidth / (2 * mount.value.clientHeight)
  camera.updateProjectionMatrix()
  secondaryCamera.updateProjectionMatrix()
  renderer.setSize(mount.value.clientWidth, mount.value.clientHeight)
}

const changeColor = () => {
  const objectToKeepOpaque = 'mesh_916'
  model.traverse((object) => {
    if (object.material && object.name !== objectToKeepOpaque) {
      object.visible = false
    }
  })

  renderer.render(scene, camera)
}
const changeColor2 = () => {
  const objectToKeepOpaque = 'mesh_916'
  model.traverse((object) => {
    if (object.material && object.name !== objectToKeepOpaque) {
      object.visible = true
    }
  })

  renderer.render(scene, camera)
}
</script>

<style scoped>
.model-box {
  cursor: pointer;
  display: flex;
  width: 30%;
  height: 50px;
  align-items: center;
  justify-content: space-between;
}

.model-container {
  width: 100%;
  height: 100%;
}
</style>
