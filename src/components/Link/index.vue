<template>
  <a
    :class="{
      link: true,
      link_disabled: disabled,
      [`link_line_${underline}`]: true,
    }"
    :style="{
      '--colorTheme': `var(--${type})`,
      '--colorThemeFaded': `var(--${type}Faded)`,
    }"
    @click="linkClickListener"
  >
    <p
      :class="{
        'link__content-inner': true,
        'link__content-inner_reversed': rightIcon,
      }"
    >
      <Icon v-if="!!icon.length" :icon="icon" />
      <span
        :class="{
          'link__content-inner__content': !!icon.length && !!$slots.default,
        }"
      >
        <slot></slot>
      </span>
    </p>
  </a>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Icon } from "@iconify/vue";

export default defineComponent({
  name: "LinkComponent",

  props: {
    type: { type: String, default: "primary" },
    icon: { type: String, default: "" },
    underline: { type: String, default: "center" },
    rightIcon: { type: Boolean, default: false },
    disabled: { type: Boolean, default: false },
  },

  components: { Icon },

  methods: {
    linkClickListener(event: Event): void {
      if (this.disabled) event.preventDefault();
    },
  },
});
</script>

<style>
.link {
  font-size: 1em;

  appearance: none;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: fit-content;
  text-decoration: none;

  color: var(--colorTheme);
}

.link_disabled {
  opacity: 0.6;
}

.link::after {
  content: "";
  width: 100%;
  height: 1px;
  margin-top: 0.25em;
  background-color: var(--colorTheme);

  transform: scaleX(0);
  transition: 0.1s linear;
}

.link:hover:not(.link_disabled)::after {
  transform: scaleX(1);
}

.link_line_left::after {
  transform-origin: left;
}

.link_line_center::after {
  transform-origin: center;
}

.link_line_right::after {
  transform-origin: right;
}

.link_line_none::after {
  display: none;
}

.link__content-inner_reversed {
  display: flex;
  justify-content: center;
  align-items: center;
  width: fit-content;
  margin: 0;
  font-size: inherit;
  transition: 0.1s linear;
}

.link__content-inner_reversed {
  flex-direction: row-reverse;
}

.link:hover:not(.link_disabled) p {
  opacity: 0.85;
}

.link__content-inner__content {
  margin-left: 0.5em;
  margin-right: 0.5em;
  margin-top: 0.1em;
}
</style>
