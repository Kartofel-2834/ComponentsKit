<template>
  <div
    class="input-component-inner"
    :class="{
      'with-prepend':
        (type === 'number' && !withoutIncrementButtons) || !!$slots.prepend,
      'with-append':
        (type === 'number' && !withoutIncrementButtons) || !!$slots.append,
    }"
    :style="{ '--theme': `var(--${theme})` }"
  >
    <div
      v-if="(type === 'number' && !withoutIncrementButtons) || !!$slots.prepend"
      class="prepend-part"
    >
      <slot name="prepend">
        <Button :type="theme" class="increment-button" @click="decrementButton">
          -
        </Button>
      </slot>
    </div>
    <div
      class="input-inner"
      :class="{
        disabled,
        focus,
        clearable,
        empty: modelValue?.length === 0,
        reversed: leftIcon,
        password: type === 'password',
      }"
    >
      <input
        :value="modelValue"
        :placeholder="placeholder"
        :disabled="disabled"
        :type="passwordMode ? 'password' : 'text'"
        @input="inputListener"
        @focus="focusListener"
        @blur="blurListener"
        @keydown="keydownListener"
        @keyup="keyupListener"
      />
      <slot name="icon">
        <Icon
          v-if="type === 'password' || clearable || (icon && icon.length)"
          :icon="getCurrentIcon()"
          @click="iconClickListener"
        />
        <span v-else-if="limit && !isNaN(limit) && !hideLimit">
          {{ modelValue?.length || 0 }}/{{ limit }}
        </span>
      </slot>
    </div>
    <div
      v-if="(type === 'number' && !withoutIncrementButtons) || !!$slots.append"
      class="append-part"
    >
      <slot name="append">
        <Button :type="theme" class="increment-button" @click="incrementValue">
          +
        </Button>
      </slot>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Icon } from "@iconify/vue";
import Button from "../Button/index.vue";

export default defineComponent({
  name: "InputComponent",

  emits: [
    "update:modelValue",
    "input",
    "iconClick",
    "clear",
    "focus",
    "blur",
    "keydown",
    "keyup",
  ],

  components: { Icon, Button },

  props: {
    type: { type: String, default: "text" },
    modelValue: { type: String, default: "" },
    placeholder: { type: String, default: "" },
    theme: { type: String, default: "primary" },
    clearIcon: { type: String, default: "carbon:close" },
    passwordShownIcon: { type: String, default: "ic:baseline-remove-red-eye" },
    passwordHidedIcon: { type: String, default: "mdi:eye-off" },
    limit: { type: Number, required: false },
    step: { type: Number, default: 1 },
    icon: { type: String, required: false },
    withoutIncrementButtons: { type: Boolean, default: false },
    leftIcon: { type: Boolean, default: false },
    clearable: { type: Boolean, default: false },
    disabled: { type: Boolean, default: false },
    hideLimit: { type: Boolean, default: false },
  },

  data() {
    return {
      focus: false,
      passwordMode: false,
      pressedButtons: new Set(),
    };
  },

  watch: {
    type(newType: string): void {
      this.passwordMode = newType === "password";
    },
  },

  methods: {
    getCurrentIcon(): string {
      if (this.type === "password") {
        return this.passwordMode
          ? this.passwordHidedIcon
          : this.passwordShownIcon;
      }

      if (this.clearable) return this.clearIcon;

      return this.icon || "";
    },

    incrementValue(): void {
      this.setValue(`${(+this.modelValue || 0) + this.step}`);
    },

    decrementButton(): void {
      this.setValue(`${(+this.modelValue || 0) - this.step}`);
    },

    setValue(value: string): void {
      const update: string = this.limit ? value.slice(0, this.limit) : value;

      this.$emit("update:modelValue", update);
      this.$emit("input", update);
    },

    inputListener(event: Event): void {
      if (this.disabled) {
        event.preventDefault();
        return;
      }

      this.setValue((event.target as HTMLInputElement).value);
    },

    iconClickListener(event: Event): void {
      if (this.disabled) return;

      if (this.type === "password") {
        this.passwordMode = !this.passwordMode;
      } else if (this.clearable) {
        this.setValue("");
        this.$emit("clear");
      }

      this.$emit("iconClick", event);
    },

    focusListener(event: Event): void {
      this.focus = true;
      this.$emit("focus", event);
    },

    blurListener(event: Event): void {
      this.focus = false;
      this.$emit("blur", event);
    },

    keydownListener(event: KeyboardEvent): void {
      this.pressedButtons.add(event.key);

      const controlPressed = this.pressedButtons.has("Control");
      const paste = controlPressed && this.pressedButtons.has("v");

      if (
        this.pressedButtons.has("Backspace") ||
        event.key.includes("Arrow") ||
        (controlPressed && !paste)
      ) {
        return;
      }

      if (this.type === "number" && (paste || isNaN(+event.key))) {
        event.preventDefault();
      }

      if (
        this.limit &&
        !isNaN(this.limit) &&
        this.modelValue?.length &&
        this.modelValue.length >= this.limit
      ) {
        event.preventDefault();
        return;
      }

      this.$emit("keydown", event);
    },

    keyupListener(event: KeyboardEvent): void {
      this.pressedButtons.delete(event.key);
      this.$emit("keyup", event);
    },
  },
});
</script>

<style>
.input-component-inner {
  display: flex;
  width: 100%;
}

.input-component-inner.with-prepend.with-append {
  display: grid;
  grid-template-columns: min-content auto min-content;
  border-radius: 0;
}

.input-component-inner.with-prepend {
  display: grid;
  grid-template-columns: min-content auto;
  border-radius: 0 4px 4px 0;
}

.input-component-inner.with-append {
  display: grid;
  grid-template-columns: auto min-content;
  border-radius: 4px 0 0 4px;
}

.input-component-inner.with-prepend.with-append .input-inner {
  border-radius: 0;
}

.input-component-inner.with-prepend .input-inner {
  border-radius: 0 4px 4px 0;
}

.input-component-inner.with-append .input-inner {
  border-radius: 4px 0 0 4px;
}

/* PREPEND-APPEND-PARTS */
.input-component-inner .prepend-part,
.input-component-inner .append-part {
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: default;
  height: -webkit-fill-available;
  overflow: hidden;
}

.input-component-inner .prepend-part .increment-button,
.input-component-inner .append-part .increment-button {
  height: -webkit-fill-available;
  padding: 0.5em 1em;
  transform: scale(1.1);
}

.input-component-inner .prepend-part {
  border-radius: 4px 0 0 4px;
  border-right: 0;
}

.input-component-inner .append-part {
  border-radius: 0 4px 4px 0;
  border-left: 0;
}

/* INPUT-INNER */
.input-component-inner .input-inner,
.input-component-inner .input-inner input,
.input-component-inner .input-inner svg {
  transition: 0.15s linear;
}

.input-component-inner .input-inner {
  display: flex;
  align-items: center;

  width: -webkit-fill-available;
  font-size: 1em;
  padding: 3px;
  cursor: text;
  border: 1px solid var(--blur);
  border-radius: 4px;
}

.input-component-inner .input-inner:not(.password, .clearable).reversed {
  flex-direction: row-reverse;
}

.input-component-inner .input-inner:not(.disabled):hover {
  border-color: black;
}

.input-component-inner .input-inner.focus:not(:disabled) {
  border-color: var(--theme);
}

.input-component-inner .input-inner.disabled,
.input-component-inner .input-inner input:disabled {
  opacity: 0.7;
  cursor: default;
}

/* INPUT */

.input-component-inner .input-inner input {
  font-size: 1em;
  appearance: none;
  outline: none;
  border: none;
  min-width: 1em;
  height: -webkit-fill-available;
  width: -webkit-fill-available;
  padding: 0.5em;
  caret-color: var(--theme);
}

.input-component-inner .input-inner input::selection {
  color: var(--textColor);
  background-color: var(--theme);
}

/* ICON */

.input-component-inner .input-inner svg {
  color: var(--blur);
  transform: scale(1.25);
}

.input-component-inner .input-inner.clearable svg,
.input-component-inner .input-inner.password svg {
  cursor: pointer;
}

.input-component-inner .input-inner:not(.password).clearable.empty svg {
  opacity: 0;
}

.input-component-inner .input-inner:not(.password, .clearable).reversed svg,
.input-component-inner .input-inner:not(.password, .clearable).reversed span {
  margin-left: 0.5em;
  margin-right: 0;
}

.input-component-inner .input-inner svg,
.input-component-inner .input-inner span {
  margin-right: 0.5em;
}

.input-component-inner .input-inner:not(.disabled).clearable svg:hover,
.input-component-inner .input-inner:not(.disabled, .clearable):hover svg {
  color: black;
}

.input-component-inner
  .input-inner.focus:not(.disabled, .clearable:not(.password))
  svg {
  color: var(--theme);
}
</style>
