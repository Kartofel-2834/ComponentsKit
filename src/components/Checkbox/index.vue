<template>
  <div
    class="checkbox"
    :class="{
      checkbox_active: modelValue,
      checkbox_disabled: disabled,
      checkbox_type_button: button,
    }"
    :style="{ '--theme': `var(--${theme})` }"
    @click="update"
  >
    <div class="checkbox__icon">
      <Icon icon="material-symbols:check-small-rounded" />
    </div>

    <span v-if="$slots.default || title.length">
      <slot :value="modelValue">{{ title }}</slot>
    </span>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Icon } from "@iconify/vue";

export default defineComponent({
  name: "CheckboxComponent",

  emits: ["update:modelValue", "change"],

  props: {
    title: { type: String, default: "" },
    theme: { type: String, default: "primary" },
    modelValue: { type: Boolean, default: false },
    button: { type: Boolean, default: false },
    disabled: { type: Boolean, default: false },
  },

  components: { Icon },

  methods: {
    update(): void {
      if (this.disabled) return;

      this.$emit("update:modelValue", !this.modelValue);
      this.$emit("change", !this.modelValue);
    },
  },
});
</script>

<style>
.checkbox {
  display: flex;
  align-items: center;
  width: fit-content;
  cursor: pointer;
}

.checkbox_disabled {
  opacity: 0.7;
  cursor: default;
}

.checkbox span {
  margin-left: 0.5em;
}

.checkbox__icon {
  display: flex;
  justify-content: center;
  align-items: center;

  border: 1px solid var(--blur);
  border-radius: 2px;
  transition: 0.15s ease-out;
}

.checkbox__icon svg {
  color: var(--textColor);
  font-size: 0.9em;
  transform: scale(0);
  transition: 0.15s ease-out;
}

.checkbox_active .checkbox__icon {
  background-color: var(--theme);
  border-color: var(--theme);
}

.checkbox_active .checkbox__icon svg {
  transform: scale(1.25);
}

/* Button checkbox style */

.checkbox_type_button {
  padding: 0.5em 1em;
  border: 1px solid #909399;
  transition: 0.15s linear;
}

.checkbox_type_button:not(:only-child, :first-child) {
  border-left: none;
}

.checkbox_type_button.checkbox_active {
  background-color: var(--theme);
  border-color: var(--theme);
  color: white;
}

.checkbox_type_button:only-child {
  border-radius: 4px;
}

.checkbox_type_button:not(:only-child):first-child {
  border-radius: 4px 0 0 4px;
}

.checkbox_type_button:not(:only-child):last-child {
  border-radius: 0 4px 4px 0;
}

.checkbox_type_button:not(.checkbox_disabled, .checkbox_active):hover {
  color: var(--theme);
}

.checkbox_type_button .checkbox__icon {
  display: none;
}

.checkbox_type_button span {
  user-select: none;
  margin-left: 0;
}
</style>
