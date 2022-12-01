<template>
  <div
    class="input-component"
    :class="{
      'input-component_with-prepend':
        (isNumberType && !withoutIncrementButtons) || !!$slots.prepend,
      'input-component_with-append':
        (isNumberType && !withoutIncrementButtons) || !!$slots.append,
    }"
    :style="{ '--theme': `var(--${theme})` }"
  >
    <div
      v-if="(isNumberType && !withoutIncrementButtons) || !!$slots.prepend"
      class="input-component__prepend"
    >
      <slot name="prepend">
        <Button :type="theme" class="increment-button" @click="decrementButton">
          -
        </Button>
      </slot>
    </div>
    <div
      class="input-component__input-inner"
      :class="{
        'input-component__input-inner_disabled': disabled,
        'input-component__input-inner_focused': focus && !disabled,
        'input-component__input-inner_clearable':
          clearable && !disabled && !isPasswordType,
        'input-component__input-inner_empty': modelValue?.length === 0,
        'input-component__input-inner_reversed':
          leftIcon && !clearable && !isPasswordType,
        'input-component__input-inner_type_password': isPasswordType,
      }"
    >
      <input
        :value="modelValue"
        :placeholder="placeholder"
        :disabled="disabled"
        :type="passwordMode ? 'password' : 'text'"
        class="input-component__input-inner__input"
        @input="inputListener"
        @focus="focusListener"
        @blur="blurListener"
        @keydown="keydownListener"
        @keyup="keyupListener"
      />
      <slot name="content"></slot>
      <slot name="icon">
        <Icon
          v-if="isPasswordType || clearable || (icon && icon.length)"
          class="input-component__input-inner__icon"
          :icon="getCurrentIcon()"
          @click="iconClickListener"
        />
        <span v-else-if="limit && !isNaN(limit) && !hideLimit">
          {{ modelValue?.length || 0 }}/{{ limit }}
        </span>
      </slot>
    </div>
    <div
      v-if="(isNumberType && !withoutIncrementButtons) || !!$slots.append"
      class="input-component__append"
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
      isPasswordType: false,
      isNumberType: false,
      pressedButtons: new Set(),
    };
  },

  watch: {
    type(newType: string): void {
      console.log(newType);
      this.isPasswordType = newType === "password";
      this.passwordMode = this.isPasswordType;
      this.isNumberType = newType === "number";
    },
  },

  methods: {
    getCurrentIcon(): string {
      if (this.isPasswordType) {
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

      if (this.isPasswordType) {
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

      if (this.isNumberType && (paste || isNaN(+event.key))) {
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
.input-component {
  display: flex;
  width: 100%;
}

.input-component_with-prepend.input-component_with-append {
  display: grid;
  grid-template-columns: min-content auto min-content;
  border-radius: 0;
}

.input-component_with-prepend {
  display: grid;
  grid-template-columns: min-content auto;
  border-radius: 0 4px 4px 0;
}

.input-component_with-append {
  display: grid;
  grid-template-columns: auto min-content;
  border-radius: 4px 0 0 4px;
}

.input-component_with-prepend.input-component_with-append
  .input-component__input-inner {
  border-radius: 0;
}

.input-component_with-prepend .input-component__input-inner {
  border-radius: 0 4px 4px 0;
}

.input-component_with-append .input-component__input-inner {
  border-radius: 4px 0 0 4px;
}

/* PREPEND-APPEND-PARTS */

.input-component__prepend,
.input-component__append {
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: default;
  height: -webkit-fill-available;
  overflow: hidden;
}

.input-component__prepend {
  border-radius: 4px 0 0 4px;
  border-right: 0;
}

.input-component__append {
  border-radius: 0 4px 4px 0;
  border-left: 0;
}

.increment-button {
  height: -webkit-fill-available;
  padding: 0.5em 1em;
  transform: scale(1.1);
}

/* INPUT-INNER */
.input-component__input-inner,
.input-component__input-inner input,
.input-component__input-inner svg {
  transition: 0.15s linear;
}

.input-component__input-inner {
  display: flex;
  align-items: center;

  width: -webkit-fill-available;
  font-size: 1em;
  padding: 3px;
  cursor: text;
  border: 1px solid var(--blur);
  border-radius: 4px;
}

.input-component__input-inner_reversed {
  flex-direction: row-reverse;
}

.input-component__input-inner:hover {
  border-color: black;
}

.input-component__input-inner_disabled {
  opacity: 0.7;
  cursor: default;
}

.input-component__input-inner_disabled:hover {
  border-color: var(--blur);
}

.input-component__input-inner_focused,
.input-component__input-inner_focused:hover {
  border-color: var(--theme);
}

/* INPUT */
.input-component__input-inner__input {
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

.input-component__input-inner__input::selection {
  color: var(--textColor);
  background-color: var(--theme);
}

/* ICON */

.input-component__input-inner__icon {
  color: var(--blur);
  transform: scale(1.25);
}

.input-component__input-inner_clearable .input-component__input-inner__icon,
.input-component__input-inner_type_password
  .input-component__input-inner__icon {
  cursor: pointer;
}

.input-component__input-inner_clearable.input-component__input-inner_empty
  .input-component__input-inner__icon {
  opacity: 0;
}

.input-component__input-inner_reversed .input-component__input-inner__icon,
.input-component__input-inner_reversed span {
  margin-left: 0.5em;
  margin-right: 0;
}

.input-component__input-inner__icon,
.input-component__input-inner span {
  margin-right: 0.5em;
}

.input-component__input-inner:hover,
.input-component__input-inner_type_password
  .input-component__input-inner__icon:hover,
.input-component__input-inner_clearable
  .input-component__input-inner__icon:hover {
  color: black;
}

.input-component__input-inner_focused .input-component__input-inner__icon {
  color: var(--theme);
}
</style>
