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

  step({
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 20,
    theta: -Math.PI / 2,
  })

  // const branch = {
  //   start: { x: WIDTH / 2, y: HEIGHT },
  //   length: 100,
  //   theta: -Math.PI / 2,
  // }
  // const end = getEndPoint(branch)
  // drawBranch(branch)

  // const leftBranch = {
  //   start: end,
  //   length: 100,
  //   theta: branch.theta - 0.1,
  // }
  // drawBranch(leftBranch)

  // const rightBranch = {
  //   start: end,
  //   length: 100,
  //   theta: branch.theta + 0.1,
  // }
  // drawBranch(rightBranch)
}

const pendingTasks: Function[] = []

function step(b: Branch, depth = 0) {
  const end = getEndPoint(b)
  drawBranch(b)

  if (depth < 2 || Math.random() < 0.5) {
    pendingTasks.push(() => step ({
      start: end,
      length: b.length + (Math.random() * 10 - 5),
      theta: b.theta - 0.3 * Math.random(),
    }, depth + 1))
  }

  if (depth < 2 || Math.random() < 0.5) {
    pendingTasks.push(() => step ({
      start: end,
      length: b.length + (Math.random() * 10 - 5),
      theta: b.theta + 0.3 * Math.random(),
    }, depth + 1))
  }
}

function frame() {
  const tasks = [...pendingTasks]
  pendingTasks.length = 0
  tasks.forEach(fn => fn())
}

let framesCount = 0
function startFrame() {
  requestAnimationFrame(() => {
    framesCount += 1
    // 3 帧画一次
    if (framesCount % 3 === 0)
      frame()
    startFrame()
  })
}

startFrame()

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x, p1.y)
  ctx.lineTo(p2.x, p2.y)
  ctx.stroke()
}

function getEndPoint(b: Branch) {
  return {
    x: b.start.x + b.length * Math.cos(b.theta),
    y: b.start.y + b.length * Math.sin(b.theta),
  }
}

function drawBranch(l: Branch) {
  const end = getEndPoint(l)
  lineTo(l.start, end)
}

onMounted(() => {
  init()
})
</script>

<template>
  <canvas ref="el" width="600" height="600" border />
</template>
