<template>
  <Echart :options="chart_options" />
</template>

<script lang="ts" setup>
import { Echart } from '@/components/Echart/index'
import { EChartsOption } from 'echarts'

const chart_LegendData = [
  {
    name: 'THREE.JS'
  },
  {
    name: 'BIM模型'
  },
  {
    name: '建模工具'
  }
]
const chart_Data = [
  {
    id: '0',
    name: 'Myriel',
    symbolSize: 19.12381,
    x: -266.82776,
    y: 299.6904,
    value: 28.685715,
    category: 0
  },
  {
    id: '1',
    name: 'Napoleon',
    symbolSize: 2.6666666666666665,
    x: -418.08344,
    y: 446.8853,
    value: 4,
    category: 0
  },
  {
    id: '2',
    name: 'MlleBaptistine',
    symbolSize: 6.323809333333333,
    x: -212.76357,
    y: 245.29176,
    value: 9.485714,
    category: 1
  },
  {
    id: '3',
    name: 'ne',
    symbolSize: 20,
    x: 0,
    y: 0,
    value: 9.485714,
    category: 1
  }
]
const chart_LinkData = [
  // {
  //   source: '1',
  //   target: '0'
  // },
  // {
  //   source: '2',
  //   target: '0'
  // }
]

const chart_options: EChartsOption = {
  tooltip: {},
  legend: [
    {
      data: chart_LegendData
    }
  ],
  series: [
    {
      name: '',
      type: 'graph',
      layout: 'none',
      data: chart_Data,
      links: chart_LinkData,
      categories: chart_LegendData,
      roam: true,
      label: {
        show: true,
        position: 'right',
        formatter: '{b}'
      },
      labelLayout: {
        hideOverlap: true
      },
      scaleLimit: {
        min: 0.4,
        max: 2
      },
      lineStyle: {
        color: 'source',
        curveness: 0.3
      },
      draggable: true,
      left: 100,
      right: 100,
      top: 50,
      bottom: 20
    }
  ]
}
</script>

<style scoped></style>
