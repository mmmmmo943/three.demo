<!-- <template>
  <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
    <Camera :position="{ z: 10 }" />
    <Scene>
      <PointLight :position="{ y: 50, z: 50 }" />
      <Box :size="1" ref="meshC" :rotation="{ y: Math.PI / 4, z: Math.PI / 4 }">
        <LambertMaterial />
      </Box>
    </Scene>
  </Renderer>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { Box, Camera, LambertMaterial, PointLight, Renderer, Scene } from 'troisjs'

const rendererC = ref()
const meshC = ref()

onMounted(() => {
  const renderer = rendererC.value
  const mesh = meshC.value.mesh
  renderer.onBeforeRender(() => {
    mesh!.rotation.x += 0.01
  })
})
</script> -->

<template>
  <!-- <div class="box123">123</div> -->
  <div style="width: 100%; height: 800px">
    <Renderer
      ref="renderer"
      resize
      :orbit-ctrl="{ enableDamping: true, dampingFactor: 0.05 }"
      pointer
      @click="randomColors"
    >
      <Camera :position="{ z: 200 }" />
      <Scene>
        <PointLight ref="light" color="#FFC0C0" />

        <InstancedMesh ref="imesh" :count="NUM_INSTANCES">
          <DodecahedronGeometry :radius="5" />
          <SubSurfaceMaterial :props="{ vertexColors: true }" />
        </InstancedMesh>
      </Scene>
      <EffectComposer>
        <RenderPass />
        <UnrealBloomPass :strength="0.5" />
        <FXAAPass />
      </EffectComposer>
    </Renderer>
  </div>
  <div>
    <div style="width=100px,display: flex">
      <div>模块数</div>
      <el-input v-model="NUM_INSTANCES" @blur="reModelNum" />
    </div>
    <div style="width=100px,display: flex">
      <div>吸引速度</div>
      <el-input v-model="speed" @blur="reModelNum" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { Color, InstancedBufferAttribute, MathUtils, Object3D, Vector3 } from 'three'
import {
  Camera,
  DodecahedronGeometry,
  EffectComposer,
  FXAAPass,
  InstancedMesh,
  PointLight,
  Renderer,
  RenderPass,
  SubSurfaceMaterial,
  Scene,
  UnrealBloomPass
} from 'troisjs'
import chroma from 'chroma-js'

const reModelNum = () => {
  init()
}

const { randFloat: rnd, randFloatSpread: rndFS } = MathUtils
const NUM_INSTANCES = ref(2000)
const speed = ref(0.0025)
const instances = ref([])
const cscale = ref(chroma.scale(['#dd3e1b', '#0b509c']))
const target = new Vector3()
const dummyO = new Object3D()
const dummyV = new Vector3()
for (let i = 0; i < NUM_INSTANCES.value; i++) {
  instances.value.push({
    position: new Vector3(rndFS(200), rndFS(200), rndFS(200)),
    scale: rnd(0.2, 1),
    velocity: new Vector3(rndFS(2), rndFS(2), rndFS(2)),
    attraction: speed.value + rnd(0, 0.01),
    vlimit: 0.3 + rnd(0, 0.2)
  })
}
const renderer = ref()
const imesh = ref()
const light = ref()
onMounted(() => {
  init()
})

const init = () => {
  // init instanced mesh matrix
  for (let i = 0; i < NUM_INSTANCES.value; i++) {
    const { position, scale } = instances.value[i]
    dummyO.position.copy(position)
    dummyO.scale.set(scale, scale, scale)
    dummyO.updateMatrix()
    imesh.value.mesh.setMatrixAt(i, dummyO.matrix)
  }
  updateColors()
  imesh.value.mesh.instanceMatrix.needsUpdate = true
  // animate
  renderer.value.onBeforeRender(animate)
}
const animate = () => {
  const { pointer } = renderer.value.three
  target.copy(pointer.positionV3)
  light.value.light.position.copy(target)
  for (let i = 0; i < NUM_INSTANCES.value; i++) {
    const { position, scale, velocity, attraction, vlimit } = instances.value[i]
    dummyV.copy(target).sub(position).normalize().multiplyScalar(attraction)
    velocity.add(dummyV).clampScalar(-vlimit, vlimit)
    position.add(velocity)
    dummyO.position.copy(position)
    dummyO.scale.set(scale, scale, scale)
    dummyO.lookAt(dummyV.copy(position).add(velocity))
    dummyO.updateMatrix()
    imesh.value.mesh.setMatrixAt(i, dummyO.matrix)
  }
  imesh.value.mesh.instanceMatrix.needsUpdate = true
}
const randomColors = () => {
  const c1 = chroma.random()
  const c2 = chroma.random()
  cscale.value = chroma.scale([c1, c2])
  updateColors()
}
const updateColors = () => {
  const colors = []
  for (let i = 0; i < NUM_INSTANCES.value; i++) {
    const color = new Color(cscale.value(rnd(0, 1)).hex())
    colors.push(color.r, color.g, color.b)
  }
  imesh.value.mesh.geometry.setAttribute(
    'color',
    new InstancedBufferAttribute(new Float32Array(colors), 3)
  )
}
</script>

<style scoped>
.box123 {
  position: absolute;
  top: 50%;
  left: 50%;
  font-size: 100px;
  color: aliceblue;
}
</style>
