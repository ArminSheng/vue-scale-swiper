<template>
  <div>
    <div class="swiper-wrapper" flex>
      <div
        flex
        ref="swiper-container"
        @touchmove="onMove($event)"
        @touchstart="onStart($event)"
        @touchend="onTouchEnd($event)"
        :style="{
          transform: translate,
          transition: 'all ' + duration + 'ms'
        }"
        class="content">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
  let startX = 0
  let startY = 0
  let offsetX = 0
  let offsetY = 0

  let absX = 0
  let absY = 0

  const FAST_CLICK_T = 200
  const TRANSITION_T = 300
  const SLIDE_DISTANCE = 50

  let timer = ''

  export default {
    name: 'scale-swiper',
    props: {
      options: {
        default () {
          return {
            autoplay: true
          }
        }
      }
    },
    data () {
      const cw = document.body.clientWidth
      return {
        offsetX: 0,
        currentIndex: this.options.index || 0,
        clientWidth: cw * 0.804488,
        size: 0,
        timer
      }
    },

    computed: {
      translate () {
        let { offsetX, offsetY } = this
        return `translate(${offsetX}px, 0)`
      }
    },

    mounted () {
      this.initParams(this.currentIndex)
    },

    beforeDestroy () {
      clearInterval(timer)
    },

    methods: {
      autoplay () {
        timer = setInterval(() => {
          if (this.hasNext()) {
            this.next()
          } else {
            this.currentIndex = 0
            this.slide(0)
          }
        }, 3000)
      },

      addItem (cb) {
        cb(this.size)
        this.size++
      },

      next () {
        if (this.hasNext()) {
          this.slide(++this.currentIndex)
        }
      },

      prev () {
        if (this.hasPrev()) {
          this.slide(--this.currentIndex)
        }
      },

      slide (index) {
        let { clientWidth, addTransitionTime } = this
        this.isSlide = true
        this.offsetX = -index * clientWidth
        addTransitionTime(() => {
          this.isSlide = false
        })
      },

      hasPrev () {
        return this.currentIndex > 0
      },

      hasNext () {
        return this.currentIndex < this.size - 1
      },

      resetTransformation () {
        let { currentIndex, clientWidth } = this
        this.offsetY = 0
        this.offsetX = -currentIndex * clientWidth

        this.opacity = 1
        this.isSlide = false

        offsetX = 0
        absX = 0

        this.addTransitionTime()
      },

      initParams (index) {
        this.currentIndex = index || 0
        this.isSlide = false

        offsetX = 0
        absX = 0

        if (this.options.autoplay) {
          this.autoplay()
        }
      },

      addTransitionTime (callback, time = TRANSITION_T) {
        this.duration = time
        setTimeout(() => {
          this.duration = 0
          callback && callback()
        }, time)
      },

      onStart (e) {
        startX = e.touches[0].pageX
        startY = e.touches[0].pageY

        absX = 0
        absY = 0
      },

      onTouchEnd (e) {
        if (absX > SLIDE_DISTANCE) {
          offsetX > 0 ? this.prev() : this.next()
        }

        if (!this.isSlide) {
          this.resetTransformation()
        }
      },

      onMove (e) {
        let {touches} = e

        offsetY = touches[0].pageY - startY
        offsetX = touches[0].pageX - startX
        absX = Math.abs(offsetX)
        absY = Math.abs(offsetY)

        if (absY < absX) {
          e.preventDefault()
          e.stopPropagation()
          this.offsetMove(offsetX)
        }
      },

      offsetMove (offset) {
        let {
          currentIndex,
          clientWidth
        } = this

        this.offsetX = currentIndex === 0
          ? offset
          : (-currentIndex * clientWidth) + offset
      }
    }
  }
</script>

<style scoped lang="scss">
  .swiper-wrapper {
    width: 100%;
    .swiper-content {
      width: 100%;
      position: relative;
      transition-property: transform;
      box-sizing: content-box;
      overflow: visible;
    }
  }
</style>