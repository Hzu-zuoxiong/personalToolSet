<template>
  <div class="magnifier-box">
    <div
      ref="previewPhoto"
      @mousewheel="mousewheel"
    >
      <canvas
        ref="canvas"
        class="canvas"
        @mousemove="mousemove"
        @mousedown="mousedown"
      />
      <img
        v-show="false"
        ref="img"
        :src="img.picUrl"
        @load="drawBackGround"
      >
    </div>
    <div
      v-show="isShowToast"
      class="toast"
    >{{ zoom }}%</div>
  </div>
</template>

<script>
export default {
  props: {
    img: {
      type: Object,
      default: () => {}
    },
    // 放大镜半径
    originalRadius: {
      type: Number,
      default: 100
    },
    // 放大倍数
    scale: {
      type: Number,
      default: 2
    },
    // 是否展示放大镜
    isShowMagnifier: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      zoom: 100,
      isShowToast: false
    }
  },
  mounted() {
    // 是否进行拖拽
    this.isDraging = false
    const { canvas } = this.$refs
    // 图片被放大区域的中心点，也是放大镜的中心点
    this.centerPoint = {
      x: 500,
      y: 300
    }
    // 图片被放大区域
    this.originalRectangle = {
      x: 0,
      y: 0,
      width: 0,
      height: 0
    }
    // canvas 上下文
    this.context = canvas.getContext('2d')
    // 定义距离尺寸的存储池
    this.E_SIZER = {
      distX: 0,
      distY: 0,
      clientX: 0,
      clientY: 0,
      targetX: 0,
      targetY: 0
    }
    // 页面监听 mouseup 事件，避免鼠标移出组件范围拖拽事件未被取消
    document.addEventListener('mouseup', this.mouseup)
  },
  destroyed() {
    document.removeEventListener('mouseup', this.mouseup)
  },
  methods: {
    /**
     * 绘制图片背景
     */
    drawBackGround() {
      const { canvas, img } = this.$refs
      // 纵向长图
      if (img.naturalHeight >= img.naturalWidth) {
        // 最大高度 850，宽度等比例缩放
        if (img.naturalHeight > 850) {
          canvas.height = 850
          canvas.width = canvas.height * (img.naturalWidth / img.naturalHeight)
        } else {
          canvas.height = img.naturalHeight
          canvas.width = img.naturalWidth
        }
      // 横向长图
      } else if (img.naturalWidth > img.naturalHeight) {
        // 最大宽度 1200， 高度等比例缩放
        if (img.naturalWidth > 1200) {
          canvas.width = 1200
          canvas.height = canvas.width / (img.naturalWidth / img.naturalHeight)
        } else {
          canvas.height = img.naturalHeight
          canvas.width = img.naturalWidth
        }
      }
      this.context.clearRect(0, 0, canvas.width, canvas.height)
      this.context.fillStyle = '#FFFFFF'
      this.context.fillRect(0, 0, canvas.width, canvas.height)
      this.context.drawImage(img, 0, 0, canvas.width, canvas.height)
      this.drawEPCPoint()
    },
    /**
     * EPC 图片进行标记打点
     */
    drawEPCPoint() {
      if (!this.img.picPosition) return
      const { canvas, img } = this.$refs
      // 图片横向缩放
      const widthScale = canvas.width / img.naturalWidth
      // 图片纵向缩放
      const heightScale = canvas.height / img.naturalHeight
      const {
        topLeftX, topLeftY, bottomRightX, bottomRightY
      } = this.img
      // 打点标记的左上角、宽高值
      const realTopLeftX = widthScale * topLeftX
      const realTopLeftY = heightScale * topLeftY
      const pointWidth = (bottomRightX - topLeftX) * widthScale
      const pointHeight = (bottomRightY - topLeftY) * heightScale

      this.context.fillStyle = 'rgba(0, 255, 255, 0.4)'
      this.context.fillRect(realTopLeftX, realTopLeftY, pointWidth, pointHeight)
    },
    /**
     * 计算需要放大区域
     */
    calOriginalRectangle(point) {
      const { originalRectangle, originalRadius } = this
      Object.assign(originalRectangle, {
        x: point.x - originalRadius,
        y: point.y - originalRadius,
        width: originalRadius * 2,
        height: originalRadius * 2
      })
    },
    /**
     * 绘制放大镜
     */
    drawMagnifyingGlass() {
      const {
        originalRectangle, scale, centerPoint, context, originalRadius
      } = this
      const { canvas } = this.$refs
      // 放大后的区域
      const scaleGlassRectangle = {
        x: centerPoint.x - originalRadius * scale,
        y: centerPoint.y - originalRadius * scale,
        width: originalRectangle.width * scale,
        height: originalRectangle.height * scale
      }
      context.save()
      context.beginPath()
      context.arc(centerPoint.x, centerPoint.y, originalRadius, 0, Math.PI * 2, false)
      context.clip()
      context.drawImage(canvas,
        originalRectangle.x, originalRectangle.y,
        originalRectangle.width, originalRectangle.height,
        scaleGlassRectangle.x, scaleGlassRectangle.y,
        scaleGlassRectangle.width, scaleGlassRectangle.height)
      context.restore()
      context.beginPath()
      // 绘制渐变色镜子框
      const gradient = context.createRadialGradient(
        centerPoint.x, centerPoint.y, originalRadius - 5,
        centerPoint.x, centerPoint.y, originalRadius
      )
      gradient.addColorStop(0, 'rgba(0,0,0,0.2)')
      gradient.addColorStop(0.80, 'silver')
      gradient.addColorStop(0.90, 'silver')
      gradient.addColorStop(1.0, 'rgba(150,150,150,0.9)')
      context.strokeStyle = gradient
      context.lineWidth = 5
      context.arc(centerPoint.x, centerPoint.y, originalRadius, 0, Math.PI * 2, false)
      context.stroke()
    },
    /**
     * 鼠标移动，绘制放大镜
     */
    mousemove(e) {
      if (this.isDraging) {
        e.preventDefault()
        this.transformCanvas(e)
      } else if (this.isShowMagnifier) {
        this.centerPoint = this.windowToCanvas(e.clientX / (this.zoom / 100), e.clientY / (this.zoom / 100))
        this.drawBackGround()
        this.calOriginalRectangle(this.centerPoint)
        this.drawMagnifyingGlass()
      }
    },
    /**
     * 鼠标事件获得坐标一般为屏幕的或者 window 的坐标，我们需要将其装换为 canvas 的坐标。
     */
    windowToCanvas(x, y) {
      const bbox = this.$refs.canvas.getBoundingClientRect()
      return { x: x - bbox.left, y: y - bbox.top }
    },
    /**
     * 画布拖拽
     */
    transformCanvas(e) {
      const moveX = e.clientX - this.E_SIZER.distX
      const moveY = e.clientY - this.E_SIZER.distY
      this.$refs.canvas.style.transform = `translate3d(${moveX}px, ${moveY}px, 1px)`
    },
    /**
     * 鼠标滚动，放大倍数
     */
    mousewheel(e) {
      e.preventDefault()
      this.zoom = parseInt(this.$refs.previewPhoto.style.zoom, 10) || 100
      // wheelDelta的值为正（120.240...）则是鼠标向上；为负（-120，-240）则是向下。
      this.zoom += e.wheelDelta / 12
      if (this.zoom > 10) {
        this.$refs.previewPhoto.style.zoom = `${this.zoom}%`
        this.showToast()
        this.drawBackGround()
      }
      return false
    },
    /**
     * 鼠标按下，计算当前鼠标位置
     */
    mousedown(e) {
      e.preventDefault()
      const { E_SIZER } = this
      // 解析matrix的正则
      const matrix3dReg1 = /^matrix3d\((?:[-\d.]+,\s*){12}([-\d.]+),\s*([-\d.]+)(?:,\s*[-\d.]+){2}\)/
      const matrixReg = /^matrix\((?:[-\d.]+,\s*){4}([-\d.]+),\s*([-\d.]+)\)$/
      // 获取解析后的transform样式属性值
      const matrix3dSourceValue = this.getStyle(
        e.target,
        'transform'
      )
      const matrix3dArrValue = matrix3dSourceValue.match(matrix3dReg1) || matrix3dSourceValue.match(matrixReg)

      // 记录鼠标点击时的坐标
      E_SIZER.clientX = e.clientX
      E_SIZER.clientY = e.clientY
      // 记录matrix解析后的translateX & translateY的值
      E_SIZER.targetX = matrix3dArrValue[1]
      E_SIZER.targetY = matrix3dArrValue[2]

      // 计算坐标边界巨鹿
      E_SIZER.distX = E_SIZER.clientX - E_SIZER.targetX
      E_SIZER.distY = E_SIZER.clientY - E_SIZER.targetY

      this.isDraging = true
    },
    /**
     * 获取元素计算后的样式
     * @param {Element} el 目标节点
     * @param {String} attr 目标样式规则
     * @returns {string}
     * */
    getStyle(el, attr) {
      if ((typeof window.getComputedStyle) !== 'undefined') {
        return window.getComputedStyle(el, null)[attr]
      // eslint-disable-next-line valid-typeof
      } if ((typeof el.currentStyle) !== 'undefiend') {
        return el.currentStyle[attr]
      }
      return ''
    },
    /**
     * 鼠标弹起，取消拖拽
     */
    mouseup(e) {
      e.preventDefault()
      this.isDraging = false
    },
    /**
     * 显示滚动放大倍数 toast
     */
    showToast() {
      this.isShowToast = true
      setTimeout(() => {
        this.isShowToast = false
      }, 1000)
    }
  }
}
</script>

<style lang="scss" scoped>
.magnifier-box{
  position: relative;
  .canvas {
    transform: translate3d(0, 0, 1px);
    will-change: transform;
    cursor: pointer;
  }
  .toast {
    position: absolute;
    top: 50%;
    left: 50%;
    height: 20px;
    line-height: 20px;
    padding-left: 5px;
    padding-right: 5px;
    border-radius: 10px;
    color: #ffffff;
    background: rgba($color: #000000, $alpha: 0.5);
  }
}
</style>
