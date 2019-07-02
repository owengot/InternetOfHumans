<template>
  <div class="section_title">
    <div class="icon" :id="glyph"></div>
    <component :is="level" class="heading" :id="glyph"> <slot /> </component>
  </div>
</template>

<script>
/**
 * Headings are used as the titles of each major section of a page in the
 * interface. For example, templates generally use headings as their title.
 * Heading element provides an option to change the level of the heading.
 */
export default {
  name: "SectionTitle",
  status: "prototype",
  release: "1.0.0",
  props: {
    glyph: {
      type: String,
    },
    level: {
      type: String,
      default: "h1",
      validator: value => {
        return value.match(/(h1|h2|h3|h4|h5|h6)/)
      },
    },
  },
}
</script>

<style lang="scss" scoped>
.section_title {
  @include reset;
  @include stack-space($space-m);
  font-family: $font-heading;
  line-height: $line-height-xs;
  color: $color-rich-black;
  display: flex;
  align-items: center;
  margin: 30px 0;
  .icon {
    width: 50px;
    height: 50px;
    display: inline-block;
    border-radius: 10px;
    margin-right: 3px;
    &#news {
      background: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' version='1' viewBox='0 0 100 125'%3E%3Cpath fill='white' d='M57 21v-9h-5v14h5v10h-5v9h5v-5h5V21zM38 21v-9h-5v14h5v10h-5v9h5v-5h5V21zM81 55h-5v-5H14v14h5v10h5v5h5v4h4v5h24v-5h5v-4h19v-5h5V55h-5zm0 9v5h-5v5h-5V64h5v-4h5v4z'/%3E%3C/svg%3E")
        center 60% blue no-repeat;
      background-size: 55%;
    }
    &#participate {
      background: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' version='1' viewBox='0 0 100 125'%3E%3Cpath stroke='white' stroke-width='3px' fill='white' d='M64 45v10h10V45h-5zM45 45v10h10V45h-5zM26 45v10h10V45h-5z'/%3E%3Cpath fill='white' stroke='white' stroke-width='3px' d='M88 36V26h-5v-5h-4v-4H69v-5H31v5H21v4h-4v5h-5v10H7v24h5v9h5v5h4v5h10v4h33v-4H36v-5H26v-5h-5v-5h-4v-9h-5V40h5v-9h4v-5h5v-5h10v-4h28v4h10v5h5v5h4v9h5v20h-5v19h5V64h5V36zM83 83h-4v5h14v-9h-5v4z'/%3E%3Cpath fill='white' stroke='white' stroke-width='3px' d='M74 79h5v5h-5zM69 74h-5v5h10v-5z'/%3E%3C/svg%3E")
        47% 62% no-repeat blue;
      background-size: 47%;
    }
    &#events {
      background: url("data:image/svg+xml,%3Csvg height='100' width='100' xmlns='http://www.w3.org/2000/svg' version='1'%3E%3Cpath fill='blue' d='M92 35v-8h-4v-8h-3v-4h-4v-3h-8V8h-8V4H35v4h-8v4h-8v3h-4v4h-3v8H8v8H4v30h4v8h4v8h3v4h4v3h8v4h8v4h30v-4h8v-4h8v-3h4v-4h3v-8h4v-8h4V35h-4zM65 50v4H42V15h8v31h15v4z'/%3E%3C/svg%3E")
        47% 53% white no-repeat;
      background-size: 42%;
    }
    &#organisers {
      background: url("data:image/svg+xml,%3Csvg version='1' xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='white' d='M479 306v-33h33V128h-33V95h-34V61H323v34h33v33h34v33h33v145h-33v33h-34v34h34v33h122v-33h-33v-34h-34v-33zM67 61v34H33v33H0v145h33v33h34v33H33v34H0v33h122v-33h34v-34h-34v-33H89V161h33v-33h34V95h33V61z'/%3E%3Cpath fill='white' d='M356 339v-33h34V161h-34v-33h-33V95H189v33h-33v33h-34v145h34v33h33v34h-33v33h-34v45h268v-45h-34v-33h-33v-34z'/%3E%3C/svg%3E")
        center 15px blue no-repeat;
      background-size: 47%;
    }
    &#edgeryders {
      background: url("data:image/svg+xml,%3Csvg style='fill: white' viewBox='0 0 500 500' version='1' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M0 250a250 250 0 1 1 500 0 250 250 0 0 1-500 0zm250 183V250H67a183 183 0 1 1 183 183z' fill='' fill-rule='evenodd'%3E%3C/path%3E%3C/svg%3E");
    }
  }
  h1,
  h2,
  h3,
  h4 {
    display: block;
    font-size: 1.9em;
    margin: 0 0 0 10px;
    color: blue;
    padding: 0;
    &#events,
    &#edgeryders {
      color: white;
    }
  }
}
h1 {
  letter-spacing: $spacing-xs;
  font-size: $size-xxl;
  font-weight: $weight-semi-bold;
  @media #{$media-query-l} {
    font-size: $size-xxxl;
  }
}
h2 {
  letter-spacing: $spacing-s;
  font-size: $size-xxl;
  font-weight: $weight-semi-bold;
}
h3 {
  font-size: $size-xl;
  font-weight: $weight-semi-bold;
}
h4 {
  font-size: $size-l;
  font-weight: $weight-semi-bold;
}
h5 {
  font-size: $size-m;
  font-weight: $weight-normal;
}
h6 {
  font-size: $size-s;
  font-weight: $weight-normal;
}
</style>

<docs>
  ```jsx
  <div>
    <Heading>The quick brown fox</Heading>
    <Heading level="h2">The quick brown fox</Heading>
    <Heading level="h3">The quick brown fox</Heading>
    <Heading level="h4">The quick brown fox</Heading>
  </div>
  ```
</docs>
