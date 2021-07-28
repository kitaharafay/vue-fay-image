<template>
  <div
    class="fay-image"
    :class="{ 'fay-image--bordered': bordered }"
    :style="wrapperStyle"
  >
    <div class="fay-image-wrapper">
      <img
        class="fay-image-instance"
        ref="image"
        :style="[instanceStyle]"
        :src="realImageSrc"
        @click="$emit('click', src)"
      />
      <div
        class="fay-image-cover"
        :class="{
          'fay-image-cover--loading': loading,
          'fay-image-cover--loaded': loaded,
          'fay-image-cover--error': error,
        }"
      >
        <!-- load error slot -->
        <template v-if="error">
          <slot v-if="$slots.error" name="error"></slot>
          <span v-else>load error</span>
        </template>
        <!-- loading slot -->
        <template v-else>
          <slot v-if="$slots.loading" name="loading"></slot>
          <span v-else>loading</span>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import IntersectionObserver from 'intersection-observer-polyfill'
/**
 * @property {String} src 图片资源地址
 * @property {String|Number} width 宽度，单位为px（默认200px）
 * @property {String|Number} height 高度，单位为px（默认为200px）
 * @property {Number} delay 延迟加载时间，单位为ms（默认为200ms）
 * @property {Boolean} lazy 懒加载（默认不开启 false）
 * @property {Boolean} bordered 图片边框（默认没有边框 false）
 * @property {String} mode 图片加载模式（默认scaleToFill） [scaleToFill|aspectFit|aspectFill]
 */
export default {
  name: 'FImage',
  props: {
    src: { type: String, default: '' },
    width: { type: [String, Number], default: 200 },
    height: { type: [String, Number], default: 200 },
    delay: { type: Number, default: 200 },
    lazy: { type: Boolean, default: false },
    bordered: { type: Boolean, default: false },
    mode: { type: String, default: 'scaleToFill' },
  },
  data() {
    return {
      loaded: false,
      loading: false,
      error: false,
      delayTask: null,
      observer: null,
      realImageSrc: '',
      virtualImage: null,
    }
  },
  beforeDestroy() {
    this.virtualImage && this.destroyVirtualImage()
    this.delayTask && clearTimeout(this.delayTask)
    this.observer && (this.observer = null)
  },
  watch: {
    src: {
      immediate: false,
      handler() {
        this.initComponent()
      },
    },
  },
  mounted() {
    this.initComponent()
  },
  computed: {
    wrapperStyle() {
      return {
        width: /%/.test(this.width) ? this.width : `${this.width}px`,
        height: /%/.test(this.height) ? this.height : `${this.height}px`,
      }
    },
    instanceStyle() {
      switch (this.mode) {
        case 'scaleToFill':
          return {
            top: 0,
            left: 0,
            width: '100%',
            height: '100%',
          }
        case 'aspectFit':
          return {
            top: '50%',
            left: '50%',
            maxWidth: '100%',
            maxHeight: '100%',
            transform: 'translate(-50%, -50%)',
          }
        case 'aspectFill':
          return {
            top: 0,
            left: 0,
            width: '100%',
            height: '100%',
            objectFit: 'cover',
          }
        default:
          return {}
      }
    },
  },
  methods: {
    initComponent() {
      this.loading = true
      this.loaded = false
      this.error = false
      if (this.lazy) {
        this.initIntersectionObserver()
      } else {
        this.setImage()
      }
    },
    initIntersectionObserver() {
      if (this.observer) {
        this.observer = null
      }
      this.observer = new IntersectionObserver(this.intersectionObserverCb)
      this.observer.observe(this.$refs.image)
    },
    intersectionObserverCb(entries) {
      entries.forEach((entry) => {
        if (entry.isIntersecting && !this.loaded) {
          this.loadImage()
          this.observer.unobserve(entry.target)
        }
      })
    },
    destroyVirtualImage() {
      this.virtualImage.onload = null
      this.virtualImage.onerror = null
      this.virtualImage = null
    },
    loadImage() {
      if (this.virtualImage) {
        this.destroyVirtualImage()
      }
      this.virtualImage = new Image()
      this.virtualImage.onload = this.onLoadHandler
      this.virtualImage.onerror = this.onErrorHandler
      this.virtualImage.src = this.src
    },
    setImage() {
      this.realImageSrc = this.src
      this.loading = false
      this.loaded = true
    },
    setImageDelay() {
      if (this.delayTask) clearTimeout(this.delayTask)
      this.delayTask = setTimeout(() => {
        this.setImage()
      }, this.delay)
    },
    onLoadHandler() {
      if (this.delay) {
        this.setImageDelay()
      } else {
        this.setImage()
      }
    },
    onErrorHandler() {
      this.error = true
    },
  },
}
</script>

<style lang="less" scoped>
.fay-image {
  display: inline-block;
  &.fay-image--bordered {
    border: 1px solid #f5f5f5;
  }

  .fay-image-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;

    .fay-image-instance {
      position: absolute;
      z-index: 5;
    }
    .fay-image-cover {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      z-index: 20;
      transition: 0.3s all;
      opacity: 1;
      background: #f5f5f5;
      display: flex;
      justify-content: center;
      align-items: center;

      &.fay-image-cover--loading {
        background: #f5f5f5;
      }
      &.fay-image-cover--loaded {
        z-index: 4;
        opacity: 0;
      }
      &.fay-image-cover--error {
        background: #e8e8e8;
      }
    }
  }
}
</style>
