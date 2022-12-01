<template>
  <Input
    class="select-inner"
    :class="{ 'select-inner_opened': opened }"
    placeholder="Select"
    @click="() => (opened = !opened)"
    @keydown.prevent
  >
    <template #content>
      <div class="select-inner__choices-inner"></div>
    </template>

    <template #icon>
      <Icon
        icon="material-symbols:keyboard-arrow-up-rounded"
        class="select-inner_arrow-icon"
      />
    </template>
  </Input>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Icon } from "@iconify/vue";
import Input from "../Input/index.vue";

export default defineComponent({
  name: "SelectComponent",

  data() {
    return { opened: false };
  },

  components: { Input, Icon },
});
</script>

<style>
.select-inner:not(.select-inner_opened).input-component
  .input-component__input-inner_focused {
  border-color: var(--blur);
}

.select-inner:not(.select-inner_opened).input-component
  .input-component__input-inner_focused
  svg {
  color: var(--blur);
}

.select-inner_opened.input-component .input-component__input-inner {
  border-color: var(--theme);
}

.select-inner_opened.input-component svg {
  color: var(--theme);
}

.select-inner.input-component,
.select-inner.input-component .input-component__input-inner,
.select-inner.input-component input {
  cursor: pointer;
}

.select-inner.input-component .input-component__input-inner {
  display: grid;
  grid-template-columns: auto min-content;
}

.select-inner.input-component input,
.select-inner__choices-inner {
  grid-column: 1/2;
  grid-row: 1/2;
}

.select-inner.input-component input {
  caret-color: #00000000;
  z-index: 10;
}

.select-inner__choices-inner {
  display: flex;
  flex-wrap: wrap;
  z-index: 20;
}

.select-inner_arrow-icon {
  margin: 0 0.5em;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 2em;
  height: 2em;
  transition: 0.15s linear;
  color: var(--blur);
}

.select-inner_opened .select-inner_arrow-icon {
  transform: rotate(180deg);
}
</style>
