<template>
  <div
    :style="{
      transform: index === currentIndex
        ? `translate(${index * -offset + 'px'}, 0) scale(${scale})`
        : `translate(${index * -offset + 'px'}, 0) scale(${SCALED})`
    }"
    flex-box="0"
    flex="main:center cross:center"
    class="swiper-touch-item">
    <slot></slot>
  </div>
</template>

<script>
  const SCALE = 0.853
  const SCALED = 0.6824

  export default {
    name: 'scale-slide',
    data () {
      const cw = document.body.clientWidth
      const offset = (cw - cw * SCALE) / 4 + (cw - cw * SCALED) / 2

      return {
        index: 0,
        scale: SCALE,
        SCALED: SCALED,
        offset
      }
    },

    computed: {
      currentIndex () {
        return this.$parent.currentIndex
      }
    },

    mounted () {
      this.$parent.addItem(idx => {
        this.index = idx
      })
    }
  }
</script>

<style scoped lang="scss">
  .swiper-touch-item {
    width: 100%;
    transition: all 0.3s;
    overflow: hidden;
  }
</style>