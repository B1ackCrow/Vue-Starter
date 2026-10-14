<script setup>
import { computed, ref, useId } from 'vue'

const props = defineProps({
  modelValue: {
    type: [String, Number],
    default: ''
  },

  type: {
    type: String,
    default: 'text',
    validator: value => [
      'text',
      'password',
      'email',
      'number',
      'search',
      'tel',
      'url',
      'date',
      'time',
      'datetime-local'
    ].includes(value)
  },

  label: {
    type: String,
    default: ''
  },

  placeholder: {
    type: String,
    default: ''
  },

  size: {
    type: String,
    default: 'medium',
    validator: value => ['small', 'medium', 'large'].includes(value)
  },

  variant: {
    type: String,
    default: 'default',
    validator: value => ['default', 'filled', 'underline'].includes(value)
  },

  disabled: {
    type: Boolean,
    default: false
  },

  readonly: {
    type: Boolean,
    default: false
  },

  required: {
    type: Boolean,
    default: false
  },

  error: {
    type: String,
    default: ''
  },

  hint: {
    type: String,
    default: ''
  },

  clearable: {
    type: Boolean,
    default: false
  },

  maxlength: {
    type: Number,
    default: undefined
  },

  showPasswordToggle: {
    type: Boolean,
    default: false
  },

  id: {
    type: String,
    default: undefined
  }
})

const emit = defineEmits([
  'update:modelValue',
  'focus',
  'blur',
  'change',
  'clear'
])

const generatedId = useId()
const inputId = computed(() => props.id || generatedId)

const inputRef = ref(null)
const passwordVisible = ref(false)

const inputType = computed(() => {
  if (props.type === 'password' && passwordVisible.value) {
    return 'text'
  }

  return props.type
})

const hasError = computed(() => Boolean(props.error))

const canClear = computed(() => {
  return props.clearable &&
      !props.disabled &&
      !props.readonly &&
      props.modelValue !== '' &&
      props.modelValue !== null &&
      props.modelValue !== undefined
})

function handleInput(event) {
  const value = event.target.value

  emit('update:modelValue', value)
}

function handleChange(event) {
  emit('change', event)
}

function handleFocus(event) {
  emit('focus', event)
}

function handleBlur(event) {
  emit('blur', event)
}

function clear() {
  if (props.disabled || props.readonly) return

  emit('update:modelValue', '')
  emit('clear')

  inputRef.value?.focus()
}

function togglePassword() {
  passwordVisible.value = !passwordVisible.value
}

function focus() {
  inputRef.value?.focus()
}

function blur() {
  inputRef.value?.blur()
}

defineExpose({
  focus,
  blur
})
</script>

<template>
  <div
      class="app-input-wrapper"
      :class="{
      'app-input-wrapper--disabled': disabled,
      'app-input-wrapper--error': hasError
    }"
  >
    <label
        v-if="label"
        :for="inputId"
        class="app-input__label"
    >
      {{ label }}

      <span v-if="required" class="app-input__required">
        *
      </span>
    </label>

    <div
        class="app-input"
        :class="[
        `app-input--${size}`,
        `app-input--${variant}`,
        {
          'app-input--error': hasError,
          'app-input--disabled': disabled
        }
      ]"
    >
      <span
          v-if="$slots.prefix"
          class="app-input__prefix"
      >
        <slot name="prefix" />
      </span>

      <input
          :id="inputId"
          ref="inputRef"
          class="app-input__field"
          :type="inputType"
          :value="modelValue"
          :placeholder="placeholder"
          :disabled="disabled"
          :readonly="readonly"
          :required="required"
          :maxlength="maxlength"
          :aria-invalid="hasError ? 'true' : undefined"
          :aria-describedby="hasError ? `${inputId}-error` : hint ? `${inputId}-hint` : undefined"
          @input="handleInput"
          @change="handleChange"
          @focus="handleFocus"
          @blur="handleBlur"
      />

      <button
          v-if="canClear"
          type="button"
          class="app-input__action"
          :aria-label="'Очистить поле'"
          @click="clear"
      >
        ✕
      </button>

      <button
          v-if="type === 'password' && showPasswordToggle"
          type="button"
          class="app-input__action"
          :aria-label="passwordVisible ? 'Скрыть пароль' : 'Показать пароль'"
          @click="togglePassword"
      >
        {{ passwordVisible ? '◉' : '◎' }}
      </button>

      <span
          v-if="$slots.suffix"
          class="app-input__suffix"
      >
        <slot name="suffix" />
      </span>
    </div>

    <span
        v-if="hasError"
        :id="`${inputId}-error`"
        class="app-input__error"
    >
      {{ error }}
    </span>

    <span
        v-else-if="hint"
        :id="`${inputId}-hint`"
        class="app-input__hint"
    >
      {{ hint }}
    </span>
  </div>
</template>

<style scoped lang="scss">
.app-input-wrapper {
  display: flex;
  flex-direction: column;
  gap: 6px;
  width: 100%;

  &--disabled {
    opacity: 0.6;
  }
}

.app-input {
  display: flex;
  align-items: center;
  gap: 10px;

  width: 100%;
  min-width: 0;

  border: 1px solid var(--input-border, #d5d5d5);
  border-radius: var(--input-radius, 8px);

  background-color: var(--input-background, #fff);

  transition:
      border-color 0.2s ease,
      background-color 0.2s ease,
      box-shadow 0.2s ease;

  &:focus-within {
    border-color: var(--color-primary);
    box-shadow: 0 0 0 3px var(--input-focus-ring, rgba(0, 0, 0, 0.08));
  }

  // Sizes

  &--small {
    min-height: 32px;
    padding: 0 10px;
    font-size: 13px;
  }

  &--medium {
    min-height: 40px;
    padding: 0 12px;
    font-size: 14px;
  }

  &--large {
    min-height: 48px;
    padding: 0 16px;
    font-size: 16px;
  }

  // Variants

  &--filled {
    background-color: var(--input-filled-background, #f5f5f5);
    border-color: transparent;
  }

  &--underline {
    border: none;
    border-bottom: 1px solid var(--input-border, #d5d5d5);
    border-radius: 0;

    &:focus-within {
      border-bottom-color: var(--color-primary);
      box-shadow: none;
    }
  }

  // States

  &--error {
    border-color: var(--color-danger, #dc3545);

    &:focus-within {
      border-color: var(--color-danger, #dc3545);
      box-shadow: 0 0 0 3px rgba(220, 53, 69, 0.1);
    }
  }

  &--disabled {
    cursor: not-allowed;
  }

  // Elements

  &__label {
    font-size: 14px;
    font-weight: 500;
    color: var(--color-text);
  }

  &__required {
    color: var(--color-danger, #dc3545);
  }

  &__field {
    flex: 1;
    min-width: 0;
    width: 100%;

    border: none;
    outline: none;

    padding: 0;

    font: inherit;
    color: var(--color-text);
    background: transparent;

    &::placeholder {
      color: var(--input-placeholder, #999);
    }

    &:disabled {
      cursor: not-allowed;
    }

    &::-ms-reveal {
      display: none;
    }
  }

  &__prefix,
  &__suffix {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;

    color: var(--input-icon-color, #777);

    :deep(svg),
    :deep(img) {
      width: 20px;
      height: 20px;
    }
  }

  &__action {
    display: inline-flex;
    align-items: center;
    justify-content: center;

    width: 24px;
    height: 24px;

    padding: 0;
    border: none;
    border-radius: 4px;

    background: transparent;
    color: var(--input-icon-color, #777);

    font-size: 16px;

    cursor: pointer;

    &:hover {
      color: var(--color-text);
    }

    &:focus-visible {
      outline: 2px solid var(--color-primary);
      outline-offset: 2px;
    }
  }

  &__error {
    font-size: 12px;
    color: var(--color-danger, #dc3545);
  }

  &__hint {
    font-size: 12px;
    color: var(--color-text-secondary, #777);
  }
}
</style>