<template>
  <div class="picMagnifier-container">
    <h1 class="picMagnifier-text fg-text-title">图片放大镜</h1>
    <div class="fg-text-primary">描述：实现图片预览时出现放大镜效果，并且可以进行放大缩小与拖拽</div>
    <div class="fg-text-primary mar-t-10">
      博客：<a class="link" target="_bank" href="http://www.wuzuoxiong.top/2021/07/09/canvas-实现图片放大镜效果">http://www.wuzuoxiong.top/2021/07/09/canvas-实现图片放大镜效果</a>
    </div>
    <el-row>
      <el-col :span="12">
        <h2 class="fg-text-title mar-t-20">基础效果</h2>
        <img-viewer
          tooltip="图片提示语"
          :is-show-magnifier="true"
          :pic-d-t-os="picDTOs"
        />
      </el-col>
      <el-col :span="12">
        <h2 class="fg-text-title mar-t-20">示例代码
          <i :class="[showCode ? 'el-icon-caret-top' :'el-icon-caret-bottom']" style="cursor: pointer;" @click="showCode = !showCode" />
        </h2>
        <transition name="fade">
          <prism-editor
            v-show="showCode"
            v-model="code"
            readonly
            :highlight="highlighter"
            class="my-editor"
            line-numbers
          />
        </transition>
      </el-col>
    </el-row>
    <h2 class="fg-text-title">Attributes</h2>
    <el-table
      :data="tableData"
      style="width: 100%"
    >
      <el-table-column
        prop="parameter"
        label="参数"
      />
      <el-table-column
        prop="desc"
        label="说明"
      />
      <el-table-column
        prop="type"
        label="类型"
      />
      <el-table-column
        prop="OptionValue"
        label="可选值"
      />
      <el-table-column
        prop="defaultValue"
        label="默认值"
      />
    </el-table>
  </div>
</template>

<script>

// import highlighting library (you can use any library you want just return html string)
import { highlight, languages } from 'prismjs/components/prism-core'
import 'prismjs/components/prism-clike'
import 'prismjs/components/prism-javascript'
import 'prismjs/themes/prism-tomorrow.css'
import imgViewer from './img-viewer/index.vue'
export default {
  name: 'PicMagnifier',
  components: {
    imgViewer
  },
  data() {
    return {
      showCode: false,
      code: "<template>\n  <img-viewer\n    class=\"mar-t-20\"\n    tooltip=\"图片提示语\"\n    :is-show-magnifier=\"true\"\n    :pic-d-t-os=\"picDTOs\"\n  />\n</template>\n\n<script>\nexport default {\n  data() {\n    return {\n      code: 'console.log(\"Hello world\")',\n      picDTOs: [\n        {\n          'bottomRightX': 1666,\n          'bottomRightY': 1627,\n          'picPosition': '(15)',\n          'picUrl': '//pic.qipeipu.com/npic/volkswagen/1acb16b760395343084c7ceb480611e4.png',\n          'topLeftX': 1569,\n          'topLeftY': 1540\n        }, {\n          'bottomRightX': 1722,\n          'bottomRightY': 2519,\n          'picPosition': '(1)',\n          'picUrl': '//pic.qipeipu.com/npic/volkswagen/0eb949269a94201e069c8954e94adcdc.png',\n          'topLeftX': 1680,\n          'topLeftY': 2449\n        }],\n    }\n  },\n}\n<\/script>",
      picDTOs: [
        {
          'bottomRightX': 1666,
          'bottomRightY': 1627,
          'picPosition': '(15)',
          'picUrl': '//pic.qipeipu.com/npic/volkswagen/1acb16b760395343084c7ceb480611e4.png',
          'topLeftX': 1569,
          'topLeftY': 1540
        }, {
          'bottomRightX': 1722,
          'bottomRightY': 2519,
          'picPosition': '(1)',
          'picUrl': '//pic.qipeipu.com/npic/volkswagen/0eb949269a94201e069c8954e94adcdc.png',
          'topLeftX': 1680,
          'topLeftY': 2449
        }],
      tableData: [
        {
          'parameter': 'viewerStyle',
          'desc': '缩略图宽高',
          'type': 'Object',
          'OptionValue': '---',
          'defaultValue': '{width: "50px",height: "50px"}'
        }, {
          'parameter': 'tooltip',
          'desc': '缩略图的文字提示',
          'type': 'String',
          'OptionValue': '---',
          'defaultValue': '---'
        }, {
          'parameter': 'originalRadius',
          'desc': '放大镜半径',
          'type': 'Number',
          'OptionValue': '---',
          'defaultValue': '100'
        }, {
          'parameter': 'scale',
          'desc': '放大倍数',
          'type': 'Number',
          'OptionValue': '---',
          'defaultValue': '2'
        }, {
          'parameter': 'isShowMagnifier',
          'desc': '是否展示放大镜',
          'type': 'Boolean',
          'OptionValue': '---',
          'defaultValue': 'false'
        }, {
          'parameter': 'picDTOs',
          'desc': '图片数组',
          'type': 'Array',
          'OptionValue': '---',
          'defaultValue': '[{"picUrl": "//pic.qipeipu.com/npic/volkswagen/0eb949269a94201e069c8954e94adcdc.png“,}]'
        }]
    }
  },
  methods: {
    highlighter(code) {
      return highlight(code, languages.js) // languages.<insert language> to return html with markup
    }
  }
}
</script>

<style lang="scss" scoped>
.picMagnifier {
  &-container {
    margin: 30px;
  }
}
.my-editor {
  background: #2d2d2d;
  color: #ccc;

  font-family: Fira code, Fira Mono, Consolas, Menlo, Courier, monospace;
  font-size: 14px;
  line-height: 1.5;
  padding: 5px;
}
// optional
.prism-editor__textarea:focus {
  outline: none;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .8s;
}
.fade-enter, .fade-leave-to{
  opacity: 0;
}
</style>
