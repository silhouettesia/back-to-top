<template>
  <div :class="isHidden ? 'to-top--fade-out' : 'to-top--show'" @click="scrollToTop">
    <slot>
      <template v-if="btnImg"><img :src="btnImg" alt="back2top"></template>
      <template v-else><i class="icon-chevron-up" :style="[bottomPos, rightPos]"></i></template>
    </slot>
  </div>
</template>

<script>
export default {
  props: {
    scrollDuration: {
      type: Number,
      default: 1000
    },
    btnImg: {
      default: null
    },
    bottom: {
      type: [Number, String],
      default: 20
    },
    right: {
      type: [Number, String],
      default: 20
    }
  },
  computed: {
    bottomPos() {
      const pos = Number(/^\-?\d+/.exec(this.bottom));
      return {bottom: `${pos}px`};
    },
    rightPos() {
      const pos = Number(/^\-?\d+/.exec(this.right));
      return {right: `${pos}px`};
    }
  },
  data() {
    return {
      isHidden: true,
      isScrolling: false,
      scrollCount: 0,
      cosParameter: 0,
      oldTimestamp: '',
      scrollStep: '',
      scrollMargin: ''
    }
  },
  mounted() {
    this.initScrollHandle();
  },
  methods: {
    initScrollHandle() {
      window.addEventListener('scroll', () => {
        let scrollTop = window.scrollY || window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;
        if (scrollTop > 50) {
          this.isHidden = false;
        } else {
          this.isHidden = true;
        }
      });
    },
    scrollToTop() {
      if (this.isScrolling) return;
      this.isScrolling = true;
      let scrollTop = window.scrollY || window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;
      this.cosParameter = scrollTop / 2;
      if (window.performance) {
        this.oldTimestamp = performance.now();
      } else {
        this.scrollStep = Math.PI / (this.scrollDuration / 15);
      }
      (!window.requestAnimationFrame) ? setTimeout(this.scrollStepAnalog, 15) : window.requestAnimationFrame(this.scrollStepAccuracy);
    },
    scrollStepAnalog() {
      this.scrollCount += 1;
      this.scrollMargin = this.cosParameter - this.cosParameter * Math.cos(this.scrollCount * this.scrollStep);
      if (!(window.scrollY || window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop)) {
        this.scrollCount = 0;
        return;
      }
      window.scrollTo(0, (window.scrollY || window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop) - this.scrollMargin);
      setTimeout(this.scrollStepAnalog, 15);
    },
    scrollStepAccuracy(newTimestamp) {
      this.scrollCount += Math.PI / (this.scrollDuration / (newTimestamp - this.oldTimestamp));
      if (this.scrollCount >= Math.PI) window.scrollTo(0, 0);
      if (!(window.scrollY || window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop)) {
        this.scrollCount = 0;
        return;
      }
      window.scrollTo(0, Math.round(this.cosParameter + this.cosParameter * Math.cos(this.scrollCount)));
      this.oldTimestamp = newTimestamp;
      window.requestAnimationFrame(this.scrollStepAccuracy);
    }
    /*
        Explanations:
        - pi is the length/end point of the cosinus intervall (see above)
        - newTimestamp indicates the current time when callbacks queued by requestAnimationFrame begin to fire.
          (for more information see https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)
        - newTimestamp - oldTimestamp equals the duration

          a * cos (bx + c) + d                      | c translates along the x axis = 0
        = a * cos (bx) + d                          | d translates along the y axis = 1 -> only positive y values
        = a * cos (bx) + 1                          | a stretches along the y axis = cosParameter = window.scrollY / 2
        = cosParameter + cosParameter * (cos bx)    | b stretches along the x axis = scrollCount = Math.PI / (scrollDuration / (newTimestamp - oldTimestamp))
        = cosParameter + cosParameter * (cos scrollCount * x)
    */
  }
}
</script>

<style lang="less">
.to-top--show {
  visibility: visible;
  opacity: 1;
}
.to-top--fade-out {
  visibility: hidden;
  opacity: 0;
}
.to-top--show, .to-top--fade-out {
  -webkit-transition: opacity .5s 0s, visibility .5s 0s;
  transition: opacity .5s 0s, visibility .5s 0s;
}
.icon-chevron-up {
  position: fixed;
  // right: 20px;
  // bottom: 20px;
  display: block;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: rgb(0, 0, 0);
  background: rgba(0, 0, 0, 0.6);
  transition: background .2s ease-in-out;
  &:hover {
    background: rgba(0, 0, 0, 0.8)
  }
  &::before, &::after {
    content: '';
    position: absolute;
    top: 22px;
    display: block;
    width: 20px;
    height: 5px;
    background: #fff;
    border-radius: 2.5px;
  }
  &::before {
    left: 9.5px;
    transform: rotate(-45deg);
  }
  &::after {
    right: 9.5px;
    transform: rotate(45deg);
  }
}
</style>
