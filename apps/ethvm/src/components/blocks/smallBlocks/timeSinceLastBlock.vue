<template>
  <block-component :title="blockTitle" backgroundColor="color2" :value="seconds+ ' sec'" :icon-name="blockIconType" :icon-color="blockIconColor"></block-component>
</template>

<script lang="ts">
import sEvents from '@app/configs/socketEvents.json'
import Vue from 'vue'
export default Vue.extend({
  name: 'ShortDataLastBlock',
  data() {
    return {
      blockTitle: this.$i18n.t('smlBlock.time'),
      blockIconType: 'clock-o',
      blockIconColor: '#ffd800',
      lastBlockTime: 0,
      seconds: 0
    }
  },
  created() {
    if (this.$store.getters.getBlocks && this.$store.getters.getBlocks.length > 0) {
      this.lastBlockTime = this.$store.getters.getBlocks[0].getTimestamp().toDate()
    }
    this.$eventHub.$on(sEvents.pastBlocksR, () => {
      const blocks = this.$store.getters.getBlocks
      this.lastBlockTime = blocks.length > 0 ? blocks[0].getTimestamp().toDate() : 0
    })
    this.$eventHub.$on(sEvents.newBlock, block => {
      this.lastBlockTime = new Date().getTime()
    })
  },
  beforeDestroy() {
    this.$eventHub.$off([sEvents.pastBlocksR, sEvents.newBlock])
  },
  mounted() {
    setInterval(() => {
      if (!this.$store.getters.getBlocks.length) {
        this.seconds = this.$i18n.t('message.load')
        return
      }
      this.seconds = Math.ceil((new Date().getTime() - this.lastBlockTime) / 1000)
    }, 1000)
  }
})
</script>

<style scoped lang="less">
</style>
