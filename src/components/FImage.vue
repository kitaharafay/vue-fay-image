<template>
  <div class="fay-image" :style="wrapperStyle">
    <div class="fay-image-wrapper">
      <img
        class="fay-image-instance"
        ref="image"
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
import IntersectionObserver from "intersection-observer-polyfill";
export default {
  name: "FImage",
  props: {
    src: { type: String, default: "" },
    width: { type: [String, Number], default: 200 },
    height: { type: [String, Number], default: 200 },
    delay: { type: Number, default: 200 },
    immediate: { type: Boolean, default: false },
  },
  data() {
    return {
      loaded: false,
      loading: false,
      error: false,
      delayTask: null,
      observer: null,
      realImageSrc: "",
      virtualImage: null,
    };
  },
  beforeDestroy() {
    this.virtualImage && this.destroyVirtualImage();
    this.delayTask && clearTimeout(this.delayTask);
    this.observer && (this.observer = null);
  },
  watch: {
    src: {
      immediate: false,
      handler() {
        this.initComponent();
      },
    },
  },
  mounted() {
    this.initComponent();
  },
  computed: {
    wrapperStyle() {
      return {
        width: `${this.width}px`,
        height: `${this.height}px`,
      };
    },
  },
  methods: {
    initComponent() {
      this.initIntersectionObserver();
    },
    initIntersectionObserver() {
      if (!this.observer) {
        this.observer = null;
      }
      this.observer = new IntersectionObserver(this.intersectionObserverCb);
      this.observer.observe(this.$refs.image);
    },
    intersectionObserverCb(entries) {
      entries.forEach((entry) => {
        if (entry.isIntersecting && !this.loaded) {
          this.loadImage();
          this.observer.unobserve(entry.target);
        }
      });
    },
    destroyVirtualImage() {
      this.virtualImage.onload = null;
      this.virtualImage.onerror = null;
      this.virtualImage = null;
      this.realImageSrc = null;
    },
    loadImage() {
      if (this.virtualImage) {
        this.destroyVirtualImage();
      }
      this.virtualImage = new Image();
      this.virtualImage.onload = this.onLoadHandler;
      this.virtualImage.onerror = this.onErrorHandler;
      this.virtualImage.src = this.src;
    },
    setImage() {
      this.realImageSrc = this.src;
      this.loaded = true;
    },
    setImageDelay() {
      !this.delayTask &&
        (this.delayTask = setTimeout(() => {
          this.setImage();
        }, this.delay));
    },
    onLoadHandler() {
      this.loading = false;
      if (this.delay) {
        this.setImageDelay();
      } else {
        this.setImage();
      }
    },
    onErrorHandler() {
      this.error = true;
    },
  },
};
</script>

<style lang="less" scoped>
.fay-image {
  display: inline-block;
  .fay-image-wrapper {
    position: relative;
    width: 100%;
    height: 100%;

    .fay-image-instance {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 5;
    }
    .fay-image-cover {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      z-index: 10;
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
