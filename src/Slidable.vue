<template>
  <div ref="wrapper" class="multiselect__slider__wrapper" @mousemove="onMouseMove" @mouseleave="onLeaving" @mouseenter="onEntering" >
    <div v-if="withCheckbox" class="input-background" />
    <div>
      <slot />
    </div>
  </div>
</template>
<script>
const S = 1.5

export default {
  props: {
    withCheckbox: {
      default: false
    },
    enable: {
      default: true
    }
  },
  computed: {
    parent: function () {
      return this.$refs.wrapper.parentElement.parentElement.parentElement
    },
    maxWidth: function () {
      return this.parent.clientWidth
    },
    domWidth: function () {
      const vm = this.$refs.wrapper.querySelector('.multiselect__item')
      return vm.clientWidth
    }
  },
  data () {
    return {
      show: false,
      position: 0,
      direction: -1,
      interval: null
    }
  },
  watch: {
    position (prev, next) {
      const vm = this.$refs.wrapper.querySelector('.multiselect__item')
      vm.style.transform = `translateX(${this.position}px)`
    }
  },
  methods: {
    onMouseMove (event) {
      if (!this.enable) return
      const middle = this.maxWidth / 2
      const bounds = this.parent.getBoundingClientRect()
      const relativeX = event.clientX - bounds.left
      const rx = relativeX - middle
      this.direction = (rx / middle) * S
    },
    onEntering (event) {
      if (!this.enable) return
      const vm = this.$refs.wrapper.querySelector('.multiselect__item')
      const diff = vm.clientWidth - this.maxWidth
      if (diff >= 0) {
        this.translate(diff)
      }
    },
    translate (maxDistance) {
      this.interval = setInterval(() => {
        this.position += this.direction
        if (this.direction >= 0 && this.position >= 0) {
          this.position = 0
        } else if (this.direction < 0 && -(this.position + 40) >= maxDistance) {
          this.position = -(maxDistance + 40)
        }
      }, 5)
    },
    onLeaving () {
      if (!this.enable) return
      clearInterval(this.interval)
      this.position = 0
    }
  }
}
</script>

<style scoped>
.input-background {
  position: absolute;
  background: linear-gradient(270deg, #FFFFFF00 42%, #FFFFFF 63%);
  z-index: 2;
  width: 50px;
  height: 100%;
}

.multiselect__option--highlight .input-background{
  background: linear-gradient(270deg, #FFFFFF00 42%, #41b883 63%);
}
.multiselect__option--highlight.multiselect__option--selected .input-background{
  background: linear-gradient(270deg, #FFFFFF00 42%, #ff6a6a 63%);
}
.multiselect__slider__wrapper {
  position: relative;
}
</style>
