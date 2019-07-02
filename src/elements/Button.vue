<template>
  <component
    :is="type"
    :href="href"
    :type="submit"
    :class="['button', size, state, variation]"
    :style="{ boxShadow: 'inset 0 0 0 2px ' + fill, color: fill }"
  >
    <slot />
  </component>
</template>

<script>
/**
 * Buttons are generally used for interface actions. Suitable for all-purpose use.
 * Defaults to appearance that has white background with grey border.
 * Primary style should be used only once per view for main call-to-action.
 */
export default {
  name: "Button",
  status: "prototype",
  release: "3.5.0",
  props: {
    type: {
      type: String,
      default: "button",
      validator: value => {
        return value.match(/(button|a)/)
      },
    },
    size: {
      type: String,
      default: "medium",
      validator: value => {
        return value.match(/(small|medium|large)/)
      },
    },
    href: {
      type: String,
      default: null,
    },
    submit: {
      type: String,
      default: null,
      validator: value => {
        return value.match(/(null|submit)/)
      },
    },
    state: {
      type: String,
      default: null,
      validator: value => {
        return value.match(/(hover|active|focus)/)
      },
    },
    fill: {
      type: String,
      default: "black",
    },
    variation: {
      type: String,
      default: null,
    },
  },
}
</script>

<style lang="scss" scoped>
.button {
  @include reset;
  @include stack-space($space-m);
  @include inline-space($space-xs);
  will-change: transform;
  transition: all 0.2s ease;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-weight: $weight-semi-bold;
  font-size: $size-m;
  font-family: $font-text;
  line-height: $line-height-m;
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: 0;
  border-radius: $radius-default;
  background: transparent;
  cursor: pointer;
  &:hover,
  &.hover {
    transform: translateZ(0) scale(1.03);
  }
  &:active,
  &.active {
    transform: translateZ(0) scale(1);
  }

  &:focus,
  &.focus {
  }

  // For icons inside buttons
  .icon {
    float: right;
    margin: -#{$space-xs} -#{$space-xs} -#{$space-s} $space-xs/2;
    color: $color-bleu-de-france;
  }

  // Various button sizes
  &.large {
    @include inset-squish-space($space-s);
    font-size: $size-l;
  }
  &.medium {
    @include inset-squish-space($space-s);
    font-size: $size-m;
  }
  &.small {
    @include inset-squish-space($space-xs);
    font-size: $size-s;
  }

  // Burning topics button
  &.burning {
    background: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='-269 131 100 125'%3E%3Cpath fill='%23fff' d='M-200.1 163.7l-1-.8c-1.2-1-3.1-.6-3.8.8-.3.7-.6 7.7-.6 8.9v.7c0 1-.9 1.8-1.9 1.6-.8-.1-1.4-.9-1.4-1.7-.1-12-3.7-20.4-10.5-26.7-4.1-3.8-9-7.2-10.5-4.6-.4.7.2 2.2.9 4.1 1.4 4.3-2.9 11.2-9 18.1-5.7 6.5-12.8 14.5-12.8 26.4 0 16 15.1 30.5 31.7 30.5s31.7-14.5 31.7-30.5c0-10.1-4.8-20.1-12.8-26.8zm-18.9 51.5a13.1 13.1 0 0 1-9.3-22.3l7.2-8.6a2.9 2.9 0 0 1 4.3 0l7.2 8.6c2.3 2.4 3.8 5.6 3.8 9.2-.1 7.2-6 13.1-13.2 13.1z'/%3E%3C/svg%3E")
      no-repeat 6px 7px $color-bleu-de-france;
    background-size: 18.6%;
    padding-left: 8%;
    color: white;
  }

  &.edgeryders {
    background: url("data:image/svg+xml,%3Csvg style='fill: blue' viewBox='0 0 500 500' version='1' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M0 250a250 250 0 1 1 500 0 250 250 0 0 1-500 0zm250 183V250H67a183 183 0 1 1 183 183z' fill='' fill-rule='evenodd'%3E%3C/path%3E%3C/svg%3E")
      no-repeat 8px 7px;
    background-size: 1.7em;
    padding-left: 2.7em;
    color: white;
  }

  &.arrow {
    background: url("data:image/svg+xml,%3Csvg aria-hidden='true' data-prefix='fas' data-icon='arrow-circle-right' class='svg-inline--fa fa-arrow-circle-right fa-w-16' xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='white' d='M256 8a248 248 0 1 1 0 496 248 248 0 0 1 0-496zm-29 144l76 72H120c-13 0-24 11-24 24v16c0 13 11 24 24 24h183l-76 72c-10 10-10 25 0 35l11 11c9 9 24 9 34 0l132-133c10-9 10-25 0-34L272 106c-10-9-25-9-34 0l-11 11c-10 10-10 25 0 35z'/%3E%3C/svg%3E")
      no-repeat 96% 7px;
    background-size: 1.7em;
    color: white;
  }

  // Primary button
  &.primary {
    background: $color-bleu-de-france;
    color: $color-white;
    box-shadow: none;
    &:hover,
    &.hover {
      background-color: shade($color-bleu-de-france, 12%);
    }
    &:active,
    &.active {
      background-color: shade($color-bleu-de-france, 20%);
      transition: none;
    }
    &:focus {
      outline: 0;
    }
    .user-is-tabbing &:focus,
    &.focus {
    }
  }
}
</style>

<docs>
  ```jsx
  <div>
    <Button variation="primary" size="large">Primary Button</Button>
    <Button variation="primary" size="medium">Medium</Button>
    <Button variation="primary" size="small">Small</Button>
    <br />
    <Button>Default Button</Button>
    <Button state="hover">:hover</Button>
    <Button state="active">:active</Button>
    <Button state="focus">:focus</Button>
  </div>
  ```
</docs>
