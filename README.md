# vue-lite-scroll [![license](https://img.shields.io/github/license/tserkov/vue-lite-scroll.svg)]()
A lightweight Vue component for scrolling content (with sexy scrollbars).

## Install

``` bash
# npm
npm install --save vue-lite-scroll
```

``` bash
# yarn
yarn add vue-lite-scroll
```

## Use

```html
<template>
  <vue-lite-scroll class="scroll-area">
    <img src="http://via.placeholder.com/200x500/111111/eeeeee?text=Scroll+me!">
  </vue-lite-scroll>
</template>

<script>
  import VueLiteScroll from 'vue-lite-scroll';

  export default {
    // ...
    components: {
      VueLiteScroll,
    },
    // ...
  };
</script>

<style scoped>
  .scroll-area {
    max-height: 100px;
  }
</style>
```

## Props
### speed
 - The number of pixels to move per mouse scroll
 - Number
 - Default: 53
### thumbWidth
 - The width of the scrollbar thumb
 - Number
 - Default: 6