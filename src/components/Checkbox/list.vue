<template>
  <div class="check-list" :class="{ 'check-list_type_buttons': buttons }">
    <Checkbox
      v-for="option in options"
      :key="option || option[field]"
      :button="buttons"
      :theme="theme"
      :disabled="disabled(option)"
      :model-value="isChecked(option)"
      @change="() => $emit('change', !isChecked(option), option)"
    >
      <slot :value="isChecked(option)" :option="option">
        {{ option[label] || String(option) }}
      </slot>
    </Checkbox>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import Checkbox from "./index.vue";

export default defineComponent({
  name: "CheckboxListComponent",

  emits: ["change"],

  props: {
    selected: {
      type: Array as PropType<Array<any>>,
      default: () => [],
    },
    options: {
      type: Array as PropType<Array<any>>,
      default: () => [],
    },
    label: { type: String, default: "label" },
    field: { type: String, default: "id" },
    theme: { type: String, default: "primary" },
    buttons: { type: Boolean, default: false },
    test: { type: Function, required: false },
    disabled: { type: Function, default: () => false },
  },

  components: { Checkbox },

  methods: {
    isChecked(option: any): boolean {
      if (typeof this.test === "function") return !!this.test(option);

      return (
        this.selected.includes(option) ||
        this.selected.includes(option[this.field])
      );
    },
  },
});
</script>

<style>
.check-list {
  font-size: 1em;

  display: grid;
  grid-auto-flow: row;
  grid-auto-columns: min-content;
  grid-auto-rows: min-content;
  gap: 1em;
}

.check-list_type_buttons {
  grid-auto-flow: column;
  gap: 0;
}
</style>
