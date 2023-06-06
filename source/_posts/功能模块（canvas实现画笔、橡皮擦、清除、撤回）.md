---
title: canvas实现画布效果（画笔、橡皮擦、清除、撤回、放大）
data: 2023-06-20 12:00
tags:
  - 功能模块
---

canvas实现画布的画笔绘画功能、橡皮擦擦除功能、撤回功能、清除画布功能、画布放大

<!-- more -->

### 组件封装（实现最基础的canavs绘画功能：画笔、橡皮擦、涂鸦笔）

<font color="#FF5E2A">1.WritingBoard.html</font>

```html
<template>
  <div class="writing-board" ref="writingBoardRef">
    <div class="blackboard" v-if="boardModel === 'black'"></div>
    <div class="whiteboard" v-if="boardModel === 'white'"></div>
    <div class="staffboard" v-if="boardModel === 'staff'"></div>

    <canvas class="canvas" ref="canvasRef"
      :style="{
        width: canvasWidth + 'px',
        height: canvasHeight + 'px',
      }"
      @mousedown="$event => handleMousedown($event)"
      @mousemove="$event => handleMousemove($event)"
      @mouseup="handleMouseup()"
      @touchstart="$event => handleMousedown($event)"
      @touchmove="$event => handleMousemove($event)"
      @touchend="handleMouseup(); mouseInCanvas = false"
      @mouseleave="handleMouseup(); mouseInCanvas = false"
      @mouseenter="mouseInCanvas = true"
    ></canvas>

    <template v-if="mouseInCanvas">
      <div 
        class="eraser"
        :style="{
          left: mouse.x - rubberSize / 2 + 'px',
          top: mouse.y - rubberSize / 2 + 'px',
          width: rubberSize + 'px',
          height: rubberSize + 'px',
        }"
        v-if="model === 'eraser'"
      ></div>
      <div 
        class="pen"
        :style="{
          left: mouse.x - penSize / 2 + 'px',
          top: mouse.y - penSize * 6 + penSize / 2 + 'px',
          color: color,
        }"
        v-if="model === 'pen'"
      >
        <IconWrite class="icon" :size="penSize * 6" v-if="model === 'pen'" />
      </div>
      <div 
        class="pen"
        :style="{
          left: mouse.x - markSize / 2 + 'px',
          top: mouse.y + 'px',
          color: color,
        }"
        v-if="model === 'mark'"
      >
        <IconHighLight class="icon" :size="markSize * 1.5" v-if="model === 'mark'" />
      </div>
    </template>
  </div>
</template>
```
<font color="#FF5E2A" id="link">2.WritingBoard.js</font>

```ts
import { computed, onMounted, onUnmounted, PropType, ref, watch } from 'vue'
const props = defineProps({
  // 笔的颜色
  color: {
    type: String,
    default: '#ffcc00',
  },
  // 画笔、橡皮擦、涂鸦笔
  model: {
    type: String as PropType<'pen' | 'eraser' | 'mark'>,
    default: 'pen',
  },
  // 画布背景色
  boardBgModel: {
    type: String as PropType<'white' | 'black' | 'staff'>,
    default: 'white'
  },
  // 画笔粗细
  penSize: {
    type: Number,
    default: 6,
  },
  // 涂鸦笔粗细
  markSize: {
    type: Number,
    default: 24,
  },
  // 橡皮擦大小
  rubberSize: {
    type: Number,
    default: 80,
  },
  // 自定义样式
  customStyle: {
    type: Object
  }
})

const emit = defineEmits<{
  (event: 'end'): void
}>()

let ctx: CanvasRenderingContext2D | null = null
const writingBoardRef = ref<HTMLElement>()
const canvasRef = ref<HTMLCanvasElement>()

let lastPos = {
  x: 0,
  y: 0,
}
let isMouseDown = false
let lastTime = 0
let lastLineWidth = -1

// 鼠标位置坐标：用于画笔或橡皮位置跟随
const mouse = ref({
  x: 0,
  y: 0,
})

// 鼠标是否处在画布范围内：处在范围内才会显示画笔或橡皮
const mouseInCanvas = ref(false)

// 监听更新canvas尺寸
const canvasWidth = ref(0)
const canvasHeight = ref(0)

const widthScale = computed(() => canvasRef.value ? canvasWidth.value / canvasRef.value.width : 1)
const heightScale = computed(() => canvasRef.value ? canvasHeight.value / canvasRef.value.height : 1)

const updateCanvasSize = () => {
  if (!writingBoardRef.value) return
  canvasWidth.value = writingBoardRef.value.clientWidth > props.customStyle?.width.split('px')[0] ? props.customStyle?.width.split('px')[0] : writingBoardRef.value.clientWidth
  canvasHeight.value = writingBoardRef.value.clientHeight > props.customStyle?.height.split('px')[0] ? props.customStyle?.height.split('px')[0] : writingBoardRef.value.clientHeight
}
const resizeObserver = new ResizeObserver(updateCanvasSize)
onMounted(() => {
  if (writingBoardRef.value) resizeObserver.observe(writingBoardRef.value)
})
onUnmounted(() => {
  if (writingBoardRef.value) resizeObserver.unobserve(writingBoardRef.value)
})

// 初始化画布
const initCanvas = () => {
  if (!canvasRef.value || !writingBoardRef.value) return

  ctx = canvasRef.value.getContext('2d')
  if (!ctx) return

  canvasRef.value.width = writingBoardRef.value.clientWidth
  canvasRef.value.height = writingBoardRef.value.clientHeight

  ctx.lineCap = 'round'
  ctx.lineJoin = 'round'
}
onMounted(initCanvas)

// 切换画笔模式时，更新 canvas ctx 配置
const updateCtx = () => {
  if (!ctx) return
  if (props.model === 'mark') {
    ctx.globalCompositeOperation = 'xor'
    ctx.globalAlpha = 0.5
  }
  else if (props.model === 'pen') {
    ctx.globalCompositeOperation = 'source-over'
    ctx.globalAlpha = 1
  }
}
watch(() => props.model, updateCtx)

// 绘制画笔墨迹方法
const draw = (posX: number, posY: number, lineWidth: number) => {
  if (!ctx) return

  const lastPosX = lastPos.x
  const lastPosY = lastPos.y

  ctx.lineWidth = lineWidth
  ctx.strokeStyle = props.color
  ctx.beginPath()
  ctx.moveTo(lastPosX, lastPosY)
  ctx.lineTo(posX, posY)
  ctx.stroke()
  ctx.closePath()
}

// 擦除墨迹方法
const erase = (posX: number, posY: number) => {
  if (!ctx || !canvasRef.value) return
  const lastPosX = lastPos.x
  const lastPosY = lastPos.y

  const radius = props.rubberSize / 2

  const sinRadius = radius * Math.sin(Math.atan((posY - lastPosY) / (posX - lastPosX)))
  const cosRadius = radius * Math.cos(Math.atan((posY - lastPosY) / (posX - lastPosX)))
  const rectPoint1: [number, number] = [lastPosX + sinRadius, lastPosY - cosRadius]
  const rectPoint2: [number, number] = [lastPosX - sinRadius, lastPosY + cosRadius]
  const rectPoint3: [number, number] = [posX + sinRadius, posY - cosRadius]
  const rectPoint4: [number, number] = [posX - sinRadius, posY + cosRadius]

  ctx.save()
  ctx.beginPath()
  ctx.arc(posX, posY, radius, 0, Math.PI * 2)
  ctx.clip()
  ctx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height)
  ctx.restore()

  ctx.save()
  ctx.beginPath()
  ctx.moveTo(...rectPoint1)
  ctx.lineTo(...rectPoint3)
  ctx.lineTo(...rectPoint4)
  ctx.lineTo(...rectPoint2)
  ctx.closePath()
  ctx.clip()
  ctx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height)
  ctx.restore()
}

// 计算鼠标两次移动之间的距离
const getDistance = (posX: number, posY: number) => {
  const lastPosX = lastPos.x
  const lastPosY = lastPos.y
  return Math.sqrt((posX - lastPosX) * (posX - lastPosX) + (posY - lastPosY) * (posY - lastPosY))
}

// 根据鼠标两次移动之间的距离s和时间t计算绘制速度，速度越快，墨迹越细
const getLineWidth = (s: number, t: number) => {
  const maxV = 10
  const minV = 0.1
  const maxWidth = props.penSize
  const minWidth = 3
  const v = s / t
  let lineWidth

  if (v <= minV) lineWidth = maxWidth
  else if (v >= maxV) lineWidth = minWidth
  else lineWidth = maxWidth - v / maxV * maxWidth

  if (lastLineWidth === -1) return lineWidth
  return lineWidth * 1 / 3 + lastLineWidth * 2 / 3
}

// 路径操作
const handleMove = (x: number, y: number) => {
  const time = new Date().getTime()

  if (props.model === 'pen') {
    const s = getDistance(x, y)
    const t = time - lastTime
    const lineWidth = getLineWidth(s, t)

    draw(x, y, lineWidth)
    lastLineWidth = lineWidth
  }
  else if (props.model === 'mark') draw(x, y, props.markSize)
  else erase(x, y)

  lastPos = { x, y }
  lastTime = new Date().getTime()
}

// 获取鼠标在canvas中的相对位置
const getMouseOffsetPosition = (e: MouseEvent | TouchEvent) => {
  if (!canvasRef.value) return [0, 0]
  const event = e instanceof MouseEvent ? e : e.changedTouches[0]
  const canvasRect = canvasRef.value.getBoundingClientRect()
  const x = event.pageX - canvasRect.x
  const y = event.pageY - canvasRect.y
  return [x, y]
}

// 处理鼠标（触摸）事件
// 准备开始绘制/擦除墨迹（落笔）
const handleMousedown = (e: MouseEvent | TouchEvent) => {
  const [mouseX, mouseY] = getMouseOffsetPosition(e)
  const x = mouseX / widthScale.value
  const y = mouseY / heightScale.value

  isMouseDown = true
  lastPos = { x, y }
  lastTime = new Date().getTime()

  if (!(e instanceof MouseEvent)) {
    mouse.value = { x: mouseX, y: mouseY }
    mouseInCanvas.value = true
  }
}

// 开始绘制/擦除墨迹（移动）
const handleMousemove = (e: MouseEvent | TouchEvent) => {
  const [mouseX, mouseY] = getMouseOffsetPosition(e)
  const x = mouseX / widthScale.value
  const y = mouseY / heightScale.value

  mouse.value = { x: mouseX, y: mouseY }

  if (isMouseDown) handleMove(x, y)
}

// 结束绘制/擦除墨迹（停笔）
const handleMouseup = () => {
  if (!isMouseDown) return
  isMouseDown = false
  emit('end')
}

// 清空画布
const clearCanvas = () => {
  if (!ctx || !canvasRef.value) return
  ctx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height)
  emit('end')
}

// 获取 DataURL
const getImageDataURL = () => {
  return canvasRef.value?.toDataURL()
}

// 设置 DataURL（绘制图片到 canvas）
const setImageDataURL = (imageDataURL: string) => {
  if (!ctx || !canvasRef.value) return
  
  ctx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height)

  if (imageDataURL) {
    ctx.globalCompositeOperation = 'source-over'
    ctx.globalAlpha = 1

    const img = new Image()
    img.src = imageDataURL
    img.onload = () => {
      ctx!.drawImage(img, 0, 0)
      updateCtx()
    }
  }
}

defineExpose({
  clearCanvas,
  getImageDataURL,
  setImageDataURL,
})
```
<font color="#FF5E2A">3.WritingBoard.css</font>

```css
.writing-board {
  z-index: 8;
  cursor: none;
  @include absolute-0();
}
.blackboard {
  width: 100%;
  height: 100%;
  background-color: #0f392b;
}
.whiteboard {
  width: 100%;
  height: 100%;
  background-color: #fff;
}

.staffboard {
  width: 100%;
  height: 100%;
  background-image: url(@/assets/imgs/staff.jpg);
  background-size: cover;
}

.canvas {
  position: absolute;
  top: 0;
  left: 0;
}
.eraser, .pen {
  pointer-events: none;
  position: absolute;
  z-index: 9;

  .icon {
    filter: drop-shadow(2px 2px 2px #555);
  }
}
.eraser {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  box-shadow: grey 0 0 10px 2px inset;
  color: rgba($color: #555, $alpha: .75);
  filter: drop-shadow(grey 0 0 2px);
}
```
<font color="#FF5E2A">4.组件封装 components/WritingBoard.vue</font>

```ts
<template src="./canavs.html"></template>
<script src="./canavs.js"></script>
<style src="./canvas.css"></style>
```

### 使用组件（最基础的画笔功能实现）

```ts
<WritingBoard 
  :penSize="penSize"
  :markSize="markSize"
  :boardBgModel="boardBgModel"
  :color="color"
  :model="model"
  @end="hanldeWritingEnd"
/>

import WritingBoard from '@/components/WritingBoard.vue'
import { ref } from 'vue'

const penSize = ref<number>(6)
const markSize = ref<number>(24)
const colors: Array<string> = ['#FF1C1C', '#FF8C00', '#FFBD00', '#F63B88', '#640FBD', '#4683FF', '#4E9B30', '#00C79C', '#2F2F2F', '#818181', '#C1C2C2', '#FFFFFF']
const color = ref<string>('#e2534d')
const boardBgModel = ref('white' as 'white' | 'black' | 'staff')
const model = ref('pen' as 'pen' | 'eraser' | 'mark')
const hanldeWritingEnd = () => { }
```

### 添加撤回功能、清除功能、笔迹数据保存（以上使用模版添加逻辑）

```ts
<WritingBoard 
  :penSize="penSize"
  :markSize="markSize"
  :boardBgModel="boardBgModel"
  :color="color"
  :model="model"
  ref="writingBoardRef"
  @end="hanldeWritingEnd"
/>

import WritingBoard from '@/components/WritingBoard.vue'
import { ref, watch } from 'vue'

const writingBoardRef = ref<typeof WritingBoard>()
const writingData = ref([] as Array<string>) // 将每次笔迹保存，base64图片格式
const writingIndex = ref(0 as number) // 用来记录画笔次数、上一步画笔次数 -1 | 下一步画笔次数 + 1
const boardBgModel = ref('white' as 'white' | 'black' | 'staff')

// 清除画布上的墨迹
const clearCanvas = () => {
  writingBoardRef.value!.clearCanvas()
}

// 笔迹数据保存
const hanldeWritingEnd = () => {
  const dataURL = writingBoardRef.value!.getImageDataURL()
  writingData.value.push(dataURL)
  writingIndex.value += 1
}

// 撤回上一步
const withDrawnUp = () => {
  if (writingIndex.value === 0) return // 已经是第一步了
  writingIndex.value -= 1
  writingBoardRef.value!.setImageDataURL(writingData.value[writingIndex.value])
}

// 撤回下一步
const withDrawnDown = () => {
  if (writingIndex.value === writingData.value.length) return // 已经是最后一步了
  writingIndex.value += 1
  writingBoardRef.value!.setImageDataURL(writingData.value[writingIndex.value])
}

// 数据回显（白色黑板 | 黑色黑板 | 自定义板）三个黑板数据不通用隔离 ~ 省麻烦这里展示一种
watch(() => boardBgModel.value, (type: typeof boardBgModel.value) => {
  swtich (type) {
    case 'white' | 'black' | 'staff':
      writingBoardRef.value!.setImageDataURL(writingData.value[writingData.value.length - 1])
      break
    default
      writingBoardRef.value!.setImageDataURL(writingData.value[writingData.value.length - 1])
  }
}, { immediate: true })
```
<font color="red">注：</font>
<font color="red">1.画笔数据其实是一张图片、base64 转换在线 url 再进行提交</font>
<font color="red">2.提交数据是最后一步结果</font>
<font color="red">3.获取远程笔迹数据注意调用 setImageDataURL 给画布数据</font>


### 添加画布放大功能（以上使用模版添加逻辑）难点来了

<font color="red">遇到的问题：</font>
<font color="red">1.画布放大导致画笔各类工具错位</font>
<font color="#FF5E2A">原因1：放大时有外部元素干扰，例如画布组件被一层div包裹着，这时候定位就会脱离外层div，导致工具错位</font>
<font color="#FF5E2A">解决办法：将外层div等比例放大</font>
<font color="#FF5E2A">原因2：new ResizeObserver这个API导致，或者是他监听的事件触发导致</font>
<font color="#FF5E2A">解决办法：将外层div等比例放大、具体看下面代码解决</font>

<font color="red">2.放大无法选择区域、默认是画笔各类工具状态</font>
<font color="#FF5E2A">解决：将画布拖拽移动分离：右下角提供一个小窗口，里面有一个元素在固定小窗口内移动，元素移动画布跟随着移动</font>

```html
 <div class="writing-board-wrap" :style="
  {
    ...centerStyle,
    ...sildeStyle
  }">
  <!-- 为了不混淆功能实现，无关的传参我这边忽略 -->
  <WritingBoard
    :customStyle="sildeStyle"
    :style="{ left: canvasLeft + 'px', top: canvasTop + 'px' }"
  />
  <!-- 引用了ant的气泡弹框组件 | windicss原子类 -->
  <div _pos="absolute" _bottom="0" _right="120px" _flex="~ items-center" _w="150px" _h="60px" class="box-shadow" _bg="white" _border="rounded-t-[10px]" _z="9">
    <Popover trigger="hover" placement="top">
      <template #content>
        <div :style="{ width: thumbnailWidth + 'px', height: thumbnailHeight + 'px' }" _pos="relative" @mousemove="handleMouseMove" @mouseup="dragging = false">
          <!-- 画板的笔迹映射到小窗口背景 -->
          <img v-if="thumbnail" :src="thumbnail" :style="{ width: thumbnailWidth + 'px', height: thumbnailHeight + 'px' }" :draggable="false">
          <div _pos="absolute" _z="9" _border="2px solid [red]" :style="{ ...thumbnailStyle, left: posX + 'px', top: posY + 'px' }" _cursor="pointer" @mousedown="handleMouseDown" @mouseleave="dragging = false"></div>
        </div>
      </template>
      <div class="w-basis-2/3" _flex="~ items-center justify-center">
        <div class="w-basis-1/2" _flex="~ items-center justify-center col" _cursor="pointer" @click="scaleSizeChange('+')">
          <iconpark-icon _m="b-8px" name="Vector" _text="25px"></iconpark-icon>
          <div>放大</div>
        </div>
        <div class="w-basis-1/2" _flex="~ items-center justify-center col" _cursor="pointer" @click="scaleSizeChange('-')">
          <iconpark-icon _m="b-8px" name="Vector-3" _text="25px"></iconpark-icon>
          <div>缩小</div>
        </div>
      </div>
    </Popover>
  </div>
</div>
```
```ts
import WritingBoard from '@/components/WritingBoard.vue'
import { ref, watch, computed, onMounted, nextTick } from 'vue'

// 小窗口宽高
const thumbnailWidth = ref(200 as number)  
const thumbnailHeight = ref(130 as number)

// 是否可拖动、鼠标按下true、松开fasle、实现鼠标长按的效果
const dragging = ref(false as boolean)

// 画布位置 left/top 定位
const canvasLeft = ref(0 as number)
const canvasTop = ref(0 as number)

// 小窗口内展示的可移动元素 left\top 定位
const posX = ref(0 as number)
const posY = ref(0 as number)

// 记录鼠标按下时 距离浏览器X、Y轴 的距离
const dragStartX = ref(0 as number)
const dragStartY = ref(0 as number)

// 画布笔迹、这里映射到小窗口内展示
const thumbnail = ref()

// 放大倍数
const scaleSize = ref(1 as number)
const scaleSizeChange = (type: '+' | '-') => {
  if (type === '+') scaleSize.value += 0.2
  else { 
    if (scaleSize.value === 1) return
    scaleSize.value -= 0.2
  }
  // 居中展示
  // posX.value = thumbnailWidth.value / 2 - thumbnailWidth.value / scaleSize.value / 2
  // posY.value = thumbnailHeight.value / 2 - thumbnailHeight.value / scaleSize.value / 2
  // canvasLeft.value = - (posX.value / thumbnailWidth.value) * props.slideWidth * scaleSize.value
  //  canvasTop.value = - (posY.value / thumbnailHeight.value) * props.slideHeight * scaleSize.value

  // 靠左展示
  posX.value = 0
  posY.value = 0
  canvasLeft.value = 0
  canvasTop.value = 0
}

// 移动元素的宽高等比例缩放，鼠标样式
const thumbnailStyle = computed( () => {
  return {
    width: thumbnailWidth.value / scaleSize.value + 'px',
    height: thumbnailHeight.value / scaleSize.value + 'px',
    cursor: dragging.value ? 'grabbing' : 'grab'
  } 
})

// 鼠标按下 
const handleMouseDown = (event: MouseEvent) => {
  dragging.value = true
  dragStartX.value = event.clientX
  dragStartY.value = event.clientY
}

// 鼠标移动
const handleMouseMove = (event: MouseEvent) => {
  if (dragging.value) {

    // 实时获取距离浏览器 X、Y轴 的距离 - 记录的距离 = 移动的距离（对于浏览器窗口来说）
    const deltaX = event.clientX - dragStartX.value
    const deltaY = event.clientY - dragStartY.value

    // 小窗口内元素的 left、top 距离
    posX.value += deltaX
    posY.value += deltaY

    // 移动元素在左边、上边即将超出小窗口了、不让他移动、保持在小窗口内
    if (posX.value <= 0) posX.value = 0
    if (posY.value <= 0) posY.value = 0

    /* 
      移动元素在右边、下边即将超出小窗口了、不让他移动、保持在小窗口内
      公式：thumbnailWidth.value(是小窗口的宽度) - thumbnailWidth.value / scaleSize.value(移动元素的宽度) = 移动元素在小窗口内可移动的距离
      公式：高度同理
    */
    if (posX.value >= thumbnailWidth.value - thumbnailWidth.value / scaleSize.value ) posX.value = thumbnailWidth.value - thumbnailWidth.value / scaleSize.value
    if (posY.value >= thumbnailHeight.value - thumbnailHeight.value / scaleSize.value ) posY.value = thumbnailHeight.value - thumbnailHeight.value / scaleSize.value
    
    // 最后再记录鼠标位置
    dragStartX.value = event.clientX
    dragStartY.value = event.clientY

    /* 
      画布的位置 = 移动元素在小窗口内移动距离 / 小窗口宽度 * 画布的宽度 * 放大倍数 
      posX.value / thumbnailWidth.value 表示 移动元素 在 小窗口内 的百分比距离 ， 这个百分比 * 画布宽度 * 乘以放大倍数 ，就是画布的定位left距离
      top距离同理
    */ 
    canvasLeft.value = - (posX.value / thumbnailWidth.value) * props.slideWidth * scaleSize.value
    canvasTop.value = - (posY.value / thumbnailHeight.value) * props.slideHeight * scaleSize.value
  }
}

```



<font color="red">解决原因2 导致画布移动错位、工具错位</font>

```ts
const centerStyle = ref({}) 
onMounted(() => {
  nextTick(() => {
    centerStyle.value = { // 定位到水平垂直居中位置，因为黑板会因为浏览器宽度不够的时候上下会有黑边、类似电影的效果、所以需要居中
      left: window?.innerWidth / 2 - props.slideWidth * scaleSize.value / 2 + 'px',
      top: window?.innerHeight / 2 - props.slideHeight * scaleSize.value / 2 + 'px',
    }
  })
})
const sildeStyle = computed(() => {
  return { // 等比例放大宽高
    width: props.slideWidth * scaleSize.value + 'px',
    height: props.slideHeight * scaleSize.value + 'px',
  }
})
```
[回到canvas画布最初封装的组件里面, 点击跳转](#link)
```ts
/*
细心的朋友已经发现，组件封装的时候已经有这个方法,这么做的目的就是定位的时候writingBoardRef.value.clientWidth会把left距离一起计算进去
width：其实是2080px，left：1000px
writingBoardRef.value.clientWidth很奇怪变成了 width + left = 3080px
所以需要在外面计算放大的宽高赋值，这样就完美解决
*/
const updateCanvasSize = () => {
  if (!writingBoardRef.value) return
  canvasWidth.value = writingBoardRef.value.clientWidth > props.customStyle?.width.split('px')[0] ? props.customStyle?.width.split('px')[0] : writingBoardRef.value.clientWidth
  canvasHeight.value = writingBoardRef.value.clientHeight > props.customStyle?.height.split('px')[0] ? props.customStyle?.height.split('px')[0] : writingBoardRef.value.clientHeight
}
```

<!-- more -->