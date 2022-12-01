<template>
  <div
    class="radio-list"
    :class="{ 'radio-list_type_button': buttons }"
    :style="{ '--color': `var(--${theme})` }"
  >
    <div
      v-for="option in options"
      :key="option[field] || String(option)"
      class="radio-list__option"
      :class="{
        'radio-list__option_active':
          !option?.disabled && (option[field] || String(option)) === value,
        'radio-list__option_disabled': !!option?.disabled,
      }"
      @click="() => select(option[field], option)"
    >
      <div class="radio-list__option__bubble"><div></div></div>

      <slot :value="option[field] || String(option)" :option="option"></slot>
      <span v-if="!$slots.default">{{ option[label] || String(option) }}</span>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";

export default defineComponent({
  name: "RadioComponent",

  emits: ["change"],

  props: {
    value: { required: false },
    options: {
      type: Array as PropType<Array<any>>,
      default: () => [],
    },
    label: { type: String, default: "label" },
    field: { type: String, default: "id" },
    theme: { type: String, default: "primary" },
    buttons: { type: Boolean, default: false },
  },

  methods: {
    select(value: any, option: any): void {
      const update = value || option;

      if (option?.disabled || this.value === update) return;

      this.$emit("change", update, option);
    },
  },
});
</script>

<style>
.radio-list {
  font-size: 1em;

  display: grid;
  grid-auto-flow: row;
  grid-auto-columns: min-content;
  grid-auto-rows: min-content;
  gap: 1em;
}

.radio-list__option {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.radio-list__option_disabled {
  opacity: 0.7;
  cursor: default;
}

.radio-list__option_active::before {
  background-color: var(--color);
}

.radio-list__option__bubble {
  display: flex;
  justify-content: center;
  align-items: center;

  width: 0.75em;
  height: 0.75em;
  margin-right: 0.5em;

  border: 2px solid;
  border-radius: 100%;
  border-color: var(--blur);
  background-color: white;

  transition: 0.15s linear;
}

.radio-list__option__bubble div {
  width: 0.5em;
  height: 0.5em;
  background-color: white;
  border-radius: 100%;
}

.radio-list__option:hover .radio-list__option__bubble {
  border-color: var(--color);
}

.radio-list__option_disabled:hover .radio-list__option__bubble {
  border-color: var(--blur);
}

.radio-list__option_active .radio-list__option__bubble {
  background-color: var(--color);
  border-color: var(--color);
}

/* Buttons radio-list styles */

.radio-list_type_button {
  grid-auto-flow: column;
  gap: 0;
}

.radio-list_type_button .radio-list__option {
  padding: 0.5em 1em;
  border: 1px solid var(--blur);
  border-left: none;
  transition: 0.15s linear;
}

.radio-list_type_button .radio-list__option_active {
  background-color: var(--color);
  border-color: var(--color);
  color: white;
}

.radio-list_type_button .radio-list__option:only-child {
  border: 1px solid var(--blur);
  border-radius: 4px;
}

.radio-list_type_button .radio-list__option:not(:only-child):first-child {
  border-left: 1px solid var(--blur);
  border-radius: 4px 0 0 4px;
}

.radio-list_type_button .radio-list__option:not(:only-child):last-child {
  border-radius: 0 4px 4px 0;
}

.radio-list_type_button
  .radio-list__option:not(
    .radio-list__option_disabled,
    .radio-list__option_active
  ):hover {
  color: var(--color);
}

.radio-list_type_button .radio-list__option__bubble {
  display: none;
}
</style>
