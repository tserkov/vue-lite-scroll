<template>
  <div ref="container" class="container" :class="{ dragging }">
    <div ref="content" class="content" :style="contentStyle" @wheel.prevent="scroll" @touchstart.prevent="start" @touchmove.prevent="drag" @touchend.prevent="stop">
      <slot></slot>
    </div>

    <div ref="yTrack" class="vertical track" @click.self="jump" v-show="content.height > container.height">
      <div ref="yThumb" class="thumb" :style="yThumbStyle" @touchstart.prevent.stop="startDrag" @mousedown.prevent.stop="startDrag"></div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'vue-lite-scroll',
    props: {
      speed: {
        type: Number,
        default: 53,
      },
      thumbWidth: {
        type: Number,
        default: 6,
      },
    },
    data() {
      return {
        container: {
          height: 0,
        },
        content: {
          height: 0,
        },
        dragging: false,
        start: {
          y: 0,
        },
        thumb: {
          y: {
            offset: 0,
            height: 0,
          },
        },
        top: 0,
      };
    },
    methods: {
      calculateSize() {
        const { height } = window.getComputedStyle(this.$refs.container, null);

        this.container.height = parseFloat(height);
        this.content.height = this.$refs.content.children[0].clientHeight;
      },
      drag(e) {
        if (!this.dragging) {
          return;
        }

        const { pageY } = e.changedTouches ? e.changedTouches[0] : e;

        this.top = this.normalize('height', this.top + (this.content.height * ((pageY - this.start.y) / this.container.height)));
        this.start.y = pageY;
      },
      jump(e) {
        const { pageY } = e;
        const { top } = this.$refs.yTrack.getBoundingClientRect();

        this.top = this.normalize('height', this.content.height * (((pageY - top) - (this.thumb.y.height / 2)) / this.container.height));
      },
      normalize(aspect, v) {
        const limit = this.content[aspect] - this.container[aspect];

        if (v > limit) {
          return limit;
        } else if (v < 0) {
          return 0;
        }

        return v;
      },
      scroll(e) {
        // Enforce our stepped scrolling
        const delta = e.deltaY > 0 ? this.speed : this.speed * -1;

        // Only scroll if there's overflow
        if (this.content.height > this.container.height) {
          this.top = this.normalize('height', this.top + delta);
        }
      },
      startDrag(e) {
        const { pageY } = e.changedTouches ? e.changedTouches[0] : e;

        this.dragging = true;
        this.start.y = pageY;
      },
      stopDrag() {
        this.dragging = false;
      },
    },
    computed: {
      contentStyle() {
        return {
          'margin-top': `${this.top * -1}px`,
        };
      },
      yThumbStyle() {
        this.thumb.y.height = Math.max(
          (this.container.height / this.content.height) * 100,
          this.thumbWidth,
        );
        this.thumb.y.offset = (this.top / (this.content.height - this.thumb.y.height)) * 100;

        return {
          top: `${this.thumb.y.offset}%`,
          height: `${this.thumb.y.height}%`,
        };
      },
    },
    mounted() {
      setTimeout(this.calculateSize, 1);

      window.addEventListener('resize', this.calculateSize);
      document.addEventListener('mousemove', this.drag);
      document.addEventListener('touchmove', this.drag);
      document.addEventListener('mouseup', this.stopDrag);
      document.addEventListener('touchend', this.stopDrag);
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.calculateSize);
      document.removeEventListener('mousemove', this.drag);
      document.removeEventListener('touchmove', this.drag);
      document.removeEventListener('mouseup', this.stopDrag);
      document.removeEventListener('touchend', this.stopDrag);
    },
  };
</script>

<style lang="sass" scoped>
  .container
    position: relative
    margin: 0 auto
    overflow: hidden

    &:hover
      .track
        opacity: 1

    &.dragging
      .content, .thumb
        transition: none

      .track
        opacity: 1

  .content
    transition: margin-top 0.5s ease-in-out

  .track
    position: absolute
    z-index: 100000
    opacity: 0
    transition: opacity 0.2s
    background: rgba(0, 0, 0, .2)

    .thumb
      background: rgba(255, 255, 255, .2)
      transition: top 0.5s ease-in-out


    &.vertical
      top: 0
      right: 0
      width: 6px
      height: 100%

      .thumb
        top: 0
        right: 0
        width: 6px

    &.horizontal
      bottom: 0
      left: 0
      height: 6px
      width: 100%

      .thumb
        bottom: 0
        left: 0
        height: 6px

    .thumb
      position: absolute
      z-index: 100001
</style>
