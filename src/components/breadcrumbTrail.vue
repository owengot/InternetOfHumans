<template>
  <div class="contanier">
    <breadcrumb
      class="trail"
      :style="{ order: order }"
      v-bind="{
        colorGetter,
        endText,
        from: fromItem,
        itemHeight,
        itemWidth,
        items,
        spacing,
        tailWidth,
        width,
      }"
    />
  </div>
</template>
<style lang="css" scoped>
.info {
  position: absolute;
  right: 20%;
}
</style>
<script>
import breadcrumb from "./breadcrumb"

/**
 * Breadcrumb trail component displaying path between root node and current node.
 * Can be used as a "legend" slot of sunburst component.
 */
export default {
  name: "breadcrumbTrail",
  components: {
    breadcrumb,
  },
  props: {
    /**
     *  Root node
     */
    root: {
      required: false,
      type: Object,
    },
    /**
     *  Current node
     */
    current: {
      required: false,
      type: Object,
    },
    /**
     *  Reference node, parents nodes of the from node will have an opacity below 1
     */
    from: {
      required: false,
      type: Object,
    },
    /**
     *  ColorGetter exposed by sunburst
     */
    colorGetter: {
      required: true,
      type: Function,
    },
    /**
     *  Sunburst width
     */
    width: {
      required: false,
      type: Number,
    },
    /**
     *  Css Order. If 1 the slot is displayed below the sunburst, if 0 the slot is displayed on top the sunburst
     */
    order: {
      required: false,
      type: Number,
      default: 1,
    },
    /**
     *  Bread crumb item width
     */
    itemWidth: {
      required: false,
      type: Number,
      default: 230,
    },
    /**
     * Bread crumb item height
     */
    itemHeight: {
      required: false,
      type: Number,
      default: 30,
    },
    /**
     * Spacing between breadcrumb items
     */
    spacing: {
      required: false,
      type: Number,
      default: 3,
    },
    /**
     * With of tailing element
     */
    tailWidth: {
      required: false,
      type: Number,
      default: 10,
    },
  },
  computed: {
    items() {
      const { current } = this
      return !current
        ? []
        : current
            .ancestors()
            .reverse()
            .map(d => ({
              id: d.data.title + d.depth,
              data: d.data,
              title: d.data.title,
              url: d.data.url,
            }))
    },
    fromItem() {
      const { from } = this
      if (!from) {
        return null
      }
      return this.items.find(it => it.data === from.data)
    },
    endText() {
      const { current } = this
      if (!current) {
        return ""
      } else if (current.data.children) {
        return ""
      } else {
        return current.data.title
      }
    },
  },
}
</script>
<style type="text/css">
.container {
  position: relative;
  width: 100%;
  height: auto;
}
.default {
  position: absolute;
  top: 00px;
  left: 0;
  z-index: 99999;
  background: red;
}
h1 {
  float: left;
}
div /deep/ .bread-sequence {
  width: 40% !important;
  float: left;
  margin: 30px 20px !important;
}
</style>
