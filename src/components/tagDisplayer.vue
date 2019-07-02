<template>
  <div class="tagviewer" v-if="show2" ref="myid" v-bind="colorGetter">
    <h3>All posts about</h3>

    <p class="tag" v-if="percentage2">{{ percentage2 }}</p>

    <p class="tag" v-else>{{ show2 }}</p>
  </div>
</template>
<script>
/**
 * Component that display the percentage value of the current node relative to root.
 * Can be used as a "top" slot of sunburst component.
 */
export default {
  name: "tagDisplayer",
  data() {
    return {
      topvalue: "",
      leftvalue: "",
    }
  },
  props: {
    /**
     *  Root node
     */
    root: {
      required: false,
      type: Object,
    },
    order: {
      required: false,
      type: Number,
      default: 0,
    },
    /**
     *  Current node
     */
    current: {
      required: false,
      type: Object,
    },
    colorGetter: {
      required: false,
      type: Function,
    },
    /**
     *  Clicked node
     */
    clicked: {
      required: false,
      type: Object,
    },
  },
  mounted() {
    // elements have been created, so the `ref` will return an element.
    // but the elements have not necessarily been inserted into the DOM yet.
    // you can use $nextTick() to wait for that to have happened.
    // this is espeically necessary if you want to to get dimensions or position of that element.

    let tagview = this.$refs["myid"]

    window.addEventListener("mousemove", this.fn, false)
  },
  methods: {
    fn(e) {
      let parent = document.querySelector(".infornation-sunburst")
      this.leftvalue = e.pageX
      this.topvalue = e.pageY - parent.offsetTop - 150
    },
  },
  created() {
    window.addEventListener("mousemove", this.fn, false)
  },
  computed: {
    /**
     * @private
     */
    base2() {},
    percentage2() {
      if (this.current == null || this.root == null) {
        return null
      } else if (this.current.data.tag) {
        return "# " + this.current.data.tag
      } else {
        return "# " + this.current.data.title
      }
    },
    show2() {
      if (this.clicked == null || this.root == null) {
        return "select a tag"
      } else if (this.clicked.data.tag) {
        return "# " + this.clicked.data.tag
      } else {
        return "# " + this.clicked.data.title
      }
    },
  },
}
</script>
<style lang="scss" scoped>
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
.tagviewer {
  width: 100%;
  display: inline-block;
  height: 150px;
  font-size: 27px;

  z-index: 3;
  text-align: left;
  h3 {
    float: left;
    display: inline-block;
    margin: 0 0 0px 0 !important;
  }
  .tag {
    margin: 0 15px;
    overflow: hidden;
    display: block;
    padding: 5px 10px 2px;
    font-weight: bold;
    float: left;
    color: white;
    background: blue;
    border-radius: 0px !important;
  }
}
</style>
