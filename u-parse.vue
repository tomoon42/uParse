<!--**
 * for: uni-app框架下 富文本解析
 */-->

<template>
  <!--基础元素-->
  <div class="wxParse" :class="className" v-if="!loading">
    <block v-for="(node, index) of nodes" :key="index">
      <vnode :node="node"></vnode>
    </block>
  </div>
</template>

<script>
import HtmlToJson from './libs/html2json';
import vnode from './components/vnode'

export default {
  name: 'wxParse',
  props: {
    loading: {
      type: Boolean,
      default: false,
    },
    className: {
      type: String,
      default: '',
    },
    content: {
      type: String,
      default: '',
    },
    noData: {
      type: String,
      default: '',
    },
    startHandler: {
      type: Function,
      default() {
        return (node) => {
          node.attr.class = null;
          node.attr.style = null;
        };
      },
    },
    endHandler: {
      type: Function,
      default: null,
    },
    charsHandler: {
      type: Function,
      default: null,
    },
    imageProp: {
      type: Object,
      default() {
        return {
          mode: 'aspectFit',
          padding: 0,
          lazyLoad: false,
          domain: '',
        };
      },
    },
  },
  components: {
    vnode
  },
  data() {
    return {
      imageUrls: [],
    };
  },
  computed: {
    nodes() {
      const {
        content,
        noData,
        imageProp,
        startHandler,
        endHandler,
        charsHandler,
      } = this;
      const parseData = content || noData;
      const customHandler = {
        start: startHandler,
        end: endHandler,
        chars: charsHandler,
      };
      const results = HtmlToJson(parseData, customHandler, imageProp, this);
      this.imageUrls = results.imageUrls;
      console.log(results)
      return results.nodes;
    },
  },
  methods: {
    navigate(href, $event) {
      this.$emit('navigate', href, $event);
    },
    preview(src, $event) {
      if (!this.imageUrls.length) return;
      wx.previewImage({
        current: src,
        urls: this.imageUrls,
      });
      this.$emit('preview', src, $event);
    },
    removeImageUrl(src) {
      const { imageUrls } = this;
      imageUrls.splice(imageUrls.indexOf(src), 1);
    },
  },
};
</script>
