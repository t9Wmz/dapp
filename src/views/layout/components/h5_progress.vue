<template>
  <div class="c-progress">
    <div class="c-progress-outer" :style="setProgressBgStyle" ref="progress">
      <div class="c-progress-inner" :style="setProgressStyle"></div>
      <div v-if="showSlider" class="c-progress-slider" ref="slider" :style="setSliderStyle"></div>
    </div>
    <span v-if="showPerText">{{tempPercent}}%</span>
  </div>
</template>

<script>
//elemen
const colorTable = {
  success: '#13ce66',
  fail: '#ff4949',
  warning: '#e6a23c',
  default: '#409EFF'
}
export default {
  name: 'h5_progress',
  props: {
    percent: {
      type: Number,
      default: 1
    },
    showSlider: {
      type: Boolean,
      default: true
    },
    showPerText: {
      type: Boolean,
      default: true
    },
    //
    width: {
      type: Number,
      default: 8.03
    },
    bgColor: {
      type: String,
      default: '#ebeef5'
    },
    progressColor: {
      type: String,
      default: '#409EFF'
    },
    //
    sliderWidth: {
      type: Number,
      default: 0.37
    },
    //
    type: {
      type: String,
      default: colorTable.default
    }
  },
  data () {
    return {
      sliderLeft: 0, //
      progressWidth: 0, //
      tempPercent: 0
    }
  },
  computed: {
    //
    setProgressBgStyle () {
      return {
        //
        width: `${this.width + this.sliderWidth}rem`
      }
    },
    //
    setProgressStyle () {
      const color = colorTable[this.type] || this.progressColor
      return {
        width: `${this.progressWidth}rem`,
        background: color
      }
    },
    //
    setSliderStyle () {
      const color = colorTable[this.type] || this.progressColor
      return {
        border: `0.01rem solid ${color}`,
        width: `${this.sliderWidth}rem`,
        height: `${this.sliderWidth}rem`,
        left: `${this.sliderLeft}rem`
      }
    }
  },
  mounted () {
    this.sliderLeft = this.width / 100 * this.percent
    this.progressWidth = this.sliderLeft + this.sliderWidth //
    this.tempPercent = this.percent
    this.addListener()
  },
  methods: {
    addListener () {
      const slider = this.$refs.slider
      const progress = this.$refs.progress
      let isClickSlider = false
      let distance = 0 //
      progress.onclick = (e) => {
        //
        if (e.target == slider) {
          return
        }
        let curX = progress.offsetLeft
        this.sliderLeft = e.offsetX - curX
        if (this.sliderLeft <= 0) {
          this.sliderLeft = 0
        }
        if (this.sliderLeft >= this.width) {
          this.sliderLeft = this.width
        }
        this._countCurPercent()
      }
      slider.onmousedown = (e) => {
        isClickSlider = true
        let curX = slider.offsetLeft
        distance = e.pageX - curX //
      }
      progress.onmousemove = (e) => {
        if (isClickSlider) {
          //
          if ((e.pageX - distance) >= 0 && (e.pageX - distance) <= (this.width - 0)) {
            this.sliderLeft = e.pageX - distance
            this._countCurPercent()
          }
        }
      }
      progress.onmouseup = () => {
        isClickSlider = false
      }
    },
    //
    _countCurPercent () {
      this.tempPercent = Math.ceil(parseInt(this.sliderLeft / this.width * 100))
      this.progressWidth = this.sliderLeft + 0.37
      //010
      if (this.tempPercent <= 0) {
        this.progressWidth = 0
        this.sliderLeft = 0
      }
      if (this.tempPercent >= 100) {
        this.progressWidth = this.width + 0.37
        this.sliderLeft = this.width
      }
      // console.log('=========sds'+ this.tempPercent)
      this.$emit('percentChange', this.tempPercent)
    }
  }
}
</script>

<style scoped lang="scss">
.c-progress {
  $width: 8.4rem;
  $radius: 0.07rem;
  display: flex;
  align-items: center;

  span {
    margin-left: 0.2rem;
    font-size: 0.32rem;
    color: #666;
  }

  .c-progress-outer {
    width: $width;
    height: 0.21rem;
    border-radius: $radius;
    background: #ebeef5;
    position: relative;
    display: flex;
    align-items: center;

    .c-progress-inner {
      width: 1rem;
      height: 0.21rem;
      background: #409EFF;
      border-radius: $radius;
    }

    .c-progress-slider {
      width: 0.37rem;
      height: 0.37rem;
      border-radius: 50%;
      background: #0792E3;
      border: 0.01rem solid #000000;
      position: absolute;
      z-index: 10;
      left: 0.2rem;
    }
  }
}
</style>

