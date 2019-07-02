<template>
  <component :is="type" class="nav">
    <Logo size="large" fill="white" variation="A" />
    <h2>{{ title }}</h2>

    <ul class="menu">
      <li>
        <a
          v-for="(item, index) in navItems"
          :key="index"
          :href="item.href"
          :class="{ active: localActive === item.component }"
          v-html="item.name"
          v-smooth-scroll="{ duration: 1000, offset: -100 }"
        />
      </li>
    </ul>
  </component>
</template>

<script>
/**
 * Used as main page navigation in templates.
 */
export default {
  name: "NavBar",
  status: "ready",
  release: "1.0.0",
  model: {
    prop: "active",
  },
  props: {
    /**
     * The html element name used for the nav bar.
     */
    type: {
      type: String,
      default: "nav",
    },

    title: {
      type: String,
      default: "nav",
    },
    /**
     * State which tab is active when initiated (using name of the component).
     */
    active: {
      required: true,
      type: String,
    },
    /**
     * Menu items to be displayed on the nav bar.
     */
    navItems: {
      required: true,
      type: Array,
    },
  },
  computed: {
    localActive: {
      get() {
        return this.active
      },
      set(val) {
        this.$emit("input", val)
      },
    },
  },
}
</script>

<style lang="scss" scoped>
// Design Tokens with local scope
$color-nav-link: $color-bleu-de-france;
$color-nav-link-active: $color-bleu-de-france;

.nav {
  @include reset;
  font-family: $font-text;
  font-size: $size-m;
  line-height: $line-height-m;
  color: $color-white;
  display: flex;
  width: 100%;
  display: flex;
  align-items: center;
  background: #0001de;
  transition: background 1s ease;
  position: fixed;
  top: 0;
  left: 0;
  padding: 0 20px;
  z-index: 999999;
  h2 {
    flex-grow: 1;
    margin-left: 0.75em;
  }
  ul.menu {
    padding: 0 !important;
    height: 100%;
    display: inline-flex;
    li {
      list-style-type: none;
      margin: 0 !important;
      a {
        color: white;
        font-weight: bold;
        padding: 0 30px 0 10px;
        text-decoration: none;
        position: relative;
        background: url("data:image/svg+xml,%3Csvg width='130' height='318' version='1' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='white' fill-rule='evenodd'%3E%3Cpath d='M0 284l35-19 19 34-35 19zM16 231l34-19 19 34-34 19zM31 178l34-19 19 34-34 19zM46 125l34-19 19 34-34 19zM61 72l34-19 19 34-34 19zM76 19l35-19 19 34-35 19z'/%3E%3C/g%3E%3C/svg%3E")
          no-repeat 93% 55%;
        background-size: 7px;
        &:last-child {
          background: none;
          padding: 0 10px;
        }
      }
    }
  }
}

.fixed {
  background: blue;
}

@media only screen and (max-width: 860px) {
  h2 {
    display: none;
  }
  .nav {
    padding: 10px 30px;
  }
  .logo {
    margin: 0 10px 0 0;
  }
  ul.menu {
    width: 100%;
    overflow: auto;
    white-space: nowrap;
    li {
      a {
        font-size: 1.3em;
      }
    }
  }
}
</style>

<docs>
  ```jsx
  <NavBar active="Dashboard" :navItems="[
    {name: 'Dashboard', component: 'Dashboard', href: '/example/'},
    {name: 'Posts', component: 'Posts', href: '/example/'},
    {name: 'Users', component: 'Users', href: '/example/'},
    {name: 'Settings', component: 'Settings', href: '/example/'}
  ]"/>
  ```
</docs>
