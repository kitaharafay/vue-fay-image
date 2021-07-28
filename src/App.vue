<!--
 * @Date: 2021-06-30 11:07:10
 * @LastEditors: fayyang
 * @LastEditTime: 2021-07-28 18:41:56
 * @Description: 
-->
<template>
  <div id="app">
    <div class="wrapper">
      <f-image
        :src="src"
        :lazy="false"
        :bordered="true"
        mode="aspectFill"
        height="100%"
        width="100"
      />
    </div>
    <button @click="onModifyHandler">modify</button>
  </div>
</template>

<script>
import FImage from './components/FImage'
export default {
  name: 'App',
  components: { FImage },
  data() {
    return {
      src: 'https://oss.zyrc66.com/pinshe/1197317965472272912.png',
    }
  },
  methods: {
    onModifyHandler() {
      this.src = 'https://oss.zyrc66.com/pinshe/1197318210575762029.jpg'
    },
  },
}
</script>

<style lang="less" scoped>
.wrapper {
  height: 400px;
  width: 400px;
  border: 1px solid #666;
}
</style>
