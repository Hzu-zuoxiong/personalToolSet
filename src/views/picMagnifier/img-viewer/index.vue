<template>
  <div class="img-viewer">
    <el-tooltip
      effect="dark"
      :content="tooltip"
      placement="top"
    >
      <img
        class="img"
        :src="picDTOs[0].picUrl"
        :style="viewerStyle"
        @click="toggleShowViewer()"
      >
    </el-tooltip>
    <viewer
      v-if="showViewer"
      :images="picDTOs"
      :visible.sync="showViewer"
      v-bind="$props"
    />
  </div>
</template>

<script>
import { isEmpty } from 'lodash'
import viewer from './viewer.vue'

export default {
  components: {
    viewer
  },
  props: {
    viewerStyle: {
      type: Object,
      default: () => ({
        width: '50px',
        height: '50px'
      })
    },
    // 图片提示语
    tooltip: {
      type: String,
      default: ''
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
    },
    // 图片数组
    picDTOs: {
      type: Array,
      default: () => ([])
    }
  },
  data() {
    return {
      showViewer: false
    }
  },
  methods: {
    isEmpty,
    toggleShowViewer() {
      this.showViewer = !this.showViewer
    }
  }
}
</script>

<style lang="scss">
.img-viewer {
  display: inline-block;
  cursor: pointer;
  .img {
    margin-left: 10px;
  }
  img {
    background: #FFFFFF;
  }
}
</style>
