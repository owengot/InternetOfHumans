<template>
  <a :href="'http://' + network + '.com/edgeryders'" target="_blank">
    <component class="social" :id="network" v-html="svg" :style="{ width: size }" />
  </a>
</template>

<script>
const req = require.context("@/assets/icons/", true, /^\.\/.*\.svg$/)

/**
 * Icons are used to visually communicate core parts of the product and
 * available actions. They can act as wayfinding tools to help users more
 * easily understand where they are in the product.
 */
export default {
  name: "Social",
  status: "review",
  release: "1.0.0",
  props: {
    network: {
      type: String,
      default: "",
    },
    fill: {
      type: String,
      default: "rgba(0,0,0,1)",
    },
    size: {
      type: String,
      default: "50px",
    },
  },
  data() {
    return {
      svg: req("./" + this.network + ".svg").replace(/^<svg /, `<svg style="fill: ${this.fill}" `),
    }
  },
}
</script>

<style lang="scss">
.social {
  @include reset;
  display: inline-block;
  float: left;
  margin: 0 20px 0 0 !important;
  svg {
    width: 90%;
    height: 90%;
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
