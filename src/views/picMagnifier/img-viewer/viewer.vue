<template>
  <div
    class="viewer-wrapper"
    @click.self="closeViewer"
  >
    <div class="viewer-top">
      <span class="s-c-white">({{ imageIndex + 1 }}/{{ images.length }})&nbsp;</span>
      <span>
        &nbsp;图片仅供参考
      </span>
      <i
        class="el-icon-close viewer-close"
        @click="closeViewer"
      />
    </div>
    <picZoom
      class="pic-zoom"
      :img="images[imageIndex]"
      :is-show-magnifier="isShowMagnifier"
      :scale="scale"
      :original-radius="originalRadius"
    />
    <!-- 左右icon -->
    <div
      v-if="images.length > 1"
      class="arrow-left"
      :class="[imageIndex < 1 ? 'f-c-grey': 'f-c-white']"
      @click="preImage"
    >
      <i
        class="el-icon-arrow-left"
      >&#xe646;</i>
    </div>
    <div
      v-if="images.length > 1"
      class="arrow-right"
      :class="[imageIndex < images.length - 1 ? 'f-c-white': 'f-c-grey']"
      @click="nextImage"
    >
      <i
        class="el-icon-arrow-right"
      >&#xe647;</i>
    </div>
  </div>
</template>

<script>
import picZoom from './picZoom.vue'

export default {
  components: {
    picZoom
  },
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    // 图片数组
    images: {
      type: Array,
      default: () => ([])
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
      imageIndex: 0
    }
  },
  mounted() {
    document.addEventListener('keyup', this.closeViewerByESC)
  },
  destroyed() {
    document.removeEventListener('keyup', this.closeViewerByESC)
  },
  methods: {
    closeViewer() {
      this.$emit('update:visible', false)
    },
    /**
     * 预览下一张图片
     */
    nextImage() {
      if (this.imageIndex >= this.images.length - 1) return
      if (this.imageIndex < this.images.length - 1) this.imageIndex += 1
    },
    /**
     * 预览上一张图片
     */
    preImage() {
      if (this.imageIndex === 0) return
      this.imageIndex -= 1
    },
    /**
     * esc 键关闭 photoViewer
     */
    closeViewerByESC(e) {
      if (e.keyCode === 27) {
        this.closeViewer()
      }
    }
  }
}
</script>

<style lang="scss">
.viewer-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 999999;
  background-color: rgba($color: #000000, $alpha: 0.6);
  .viewer-top {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 48px;
    font-size: 14px;
    z-index: 999;
    color: #C0C4CC;
    background-color: rgba(0, 0, 0, 0.4);
    .viewer-close {
      display: inline-block;
      position: absolute;
      top: 20 px;
      right: 40px;
    }
  }
  .part-desc-wrapper {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    height: 48px;
    display: flex;
    z-index: 999;
    justify-content: center;
    align-items: center;
    background: rgba(0, 0, 0, 0.4);
    color: #ffffff;
    .part-desc {
      display: flex;
      flex-direction: row;
      .part-desc-item {
        margin-right: 30px;
        font-size: 14px
      }
    }
  }
  .arrow-left {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top: 50%;
    left: 60px;
    margin-top: -50px;
    width: 60px;
    height: 100px;
    font-size: 36px;
    text-align: center;
  }
  .arrow-right {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top: 50%;
    right: 60px;
    margin-top: -50px;
    width: 60px;
    height: 100px;
    font-size: 36px;
    text-align: center;
  }
  .f-c-white {
    color: #ffffff;
  }
  .f-c-grey {
    color: #C0C4CC;
  }
}
</style>
