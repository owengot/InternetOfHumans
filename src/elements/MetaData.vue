<template>
  <div
    :class="['info', variation]"
    :style="{ border: '2px solid ' + stroke, color: currentColor, background: fill }"
  >
    <img :src="image" v-if="image" />
    <p><slot /></p>
  </div>
</template>

<script>
const req = require.context("@/assets/icons/", true, /^\.\/.*\.svg$/)

export default {
  name: "MetaData",
  status: "review",
  release: "1.0.0",
  props: {
    stroke: {
      type: String,
      default: "rgba(0,0,0,1)",
    },
    fill: {
      type: String,
      default: "rgba(0,0,0,0)",
    },
    currentColor: {
      type: String,
      default: "white",
    },
    variation: {
      type: String,
      default: "tag",
    },
    image: {
      type: String,
      default: "",
    },
  },
}
</script>

<style lang="scss">
.info {
  @include reset;
  display: block;
  border-radius: 10px;
  height: 35px;
  float: left;
  margin: 0 10px 15px 0;
  display: inline-flex;
  align-items: center;
  padding: 10px;
  &.profile {
    padding: 0 10px 0 0;
    overflow: hidden;
  }
  img {
    width: auto;
    height: 100%;
    margin-right: 10px;
  }
  p {
    margin: 0;
    font-size: 0.8em;
  }
}
</style>

<docs>
  ```jsx
  <div>
    <Icon name="ready" aria-label="Component is ready" fill="#7cb518" />
    <Icon name="review" fill="rgb(255,186,10)" />
    <Icon name="deprecated" fill="rgb(235,59,36)" />
    <Icon name="prototype" fill="rgb(37,138,239)" />
  </div>
  ```
</docs>
