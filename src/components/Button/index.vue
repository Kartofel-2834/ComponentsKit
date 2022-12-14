<template>
  <button
    :disabled="disabled"
    :class="{
      button_rounded: rounded,
      button_faded: faded,
      button_reversed: leftIcon,
    }"
    :style="{
      '--colorTheme': `var(--${type})`,
      '--colorThemeFaded': `var(--${type}Faded)`,
    }"
  >
    <span :class="{ 'button-content': !!icon.length && !!$slots.default }">
      <slot></slot>
    </span>
    <Icon
      v-if="!!icon.length || loading"
      :icon="loading ? 'line-md:loading-twotone-loop' : icon"
    />
  </button>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Icon } from "@iconify/vue";

export default defineComponent({
  name: "ButtonComponent",

  props: {
    type: { type: String, default: "primary" },
    icon: { type: String, default: "" },
    leftIcon: { type: Boolean, default: false },
    loading: { type: Boolean, default: false },
    rounded: { type: Boolean, default: false },
    disabled: { type: Boolean, default: false },
    faded: { type: Boolean, default: false },
  },

  components: { Icon },
});
</script>

<style>
button {
  font-size: 1em;
  padding: 1em;

  appearance: none;
  cursor: pointer;
  border: 1px solid;
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: fit-content;

  background-color: var(--colorTheme);
  border-color: var(--colorTheme);
  color: var(--textColor);

  transition: 0.15s ease-out;
}

.button_reversed {
  flex-direction: row-reverse;
}

.button_faded:not(.button_faded:hover) {
  color: var(--colorTheme);
  background-color: var(--colorThemeFaded);
}

button svg {
  transform: scale(1.35);
}

button:not(.button_reversed) .button-content {
  margin-right: 0.5em;
}

.button_reversed .button-content {
  margin-left: 0.5em;
}

.button_rounded {
  border-radius: 2em;
}

button:not(.button_faded, :active):hover {
  opacity: 0.85;
}

button:active {
  transform: scale(0.98);
}
</style>
