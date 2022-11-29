<template>
  <a
    :class="{ disabled, [`line-${underline}`]: true }"
    :style="{
      '--colorTheme': `var(--${type})`,
      '--colorThemeFaded': `var(--${type}Faded)`,
    }"
    @click="linkClickListener"
  >
    <p :class="{ reversed: rightIcon }">
      <Icon v-if="!!icon.length" :icon="icon" />
      <span :class="{ 'link-content': !!icon.length && !!$slots.default }">
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
a {
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

a.disabled {
  opacity: 0.6;
}

a::after {
  content: "";
  width: 100%;
  height: 1px;
  margin-top: 0.25em;
  background-color: var(--colorTheme);

  transform: scaleX(0);
  transition: 0.1s linear;
}

a:not(a.disabled):hover::after {
  transform: scaleX(1);
}

a.line-left::after {
  transform-origin: left;
}

a.line-center::after {
  transform-origin: center;
}

a.line-right::after {
  transform-origin: right;
}

a.line-none::after {
  display: none;
}

a p {
  display: flex;
  justify-content: center;
  align-items: center;
  width: fit-content;
  margin: 0;
  font-size: inherit;
  transition: 0.1s linear;
}

a p.reversed {
  flex-direction: row-reverse;
}

a:not(a.disabled):hover p {
  opacity: 0.85;
}

a p span.link-content {
  margin-left: 0.5em;
  margin-right: 0.5em;
  margin-top: 0.1em;
}
</style>
