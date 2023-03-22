<script setup lang="ts">
const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!)

const WIDTH = 600
const HEIGHT = 600

interface Point {
  x: number
  y: number
}

interface Branch {
  start: Point
  length: number
  theta: number
}

function init() {
  ctx.strokeStyle = '#000'

  const line = {
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 100,
    theta: -Math.PI / 2,
  }
  drawBranch(line)
}

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x, p1.y)
  ctx.lineTo(p2.x, p2.y)
  ctx.stroke()
}

function drawBranch(l: Branch) {
  const { start, length, theta } = l
  const end = {
    x: start.x + length * Math.cos(theta),
    y: start.y + length * Math.sin(theta),
  }
  lineTo(start, end)
}

onMounted(() => {
  init()
})
</script>

<template>
  <canvas ref="el" width="600" height="600" border />
</template>
