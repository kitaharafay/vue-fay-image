<!--
 * @Date: 2021-06-30 11:07:10
 * @LastEditors: fayyang
 * @LastEditTime: 2021-06-30 11:35:30
 * @Description: 
-->
<template>
  <div id="app">
    <f-image :src="src" :lazy="false" :bordered="true" mode="aspectFit" />
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
