<script setup>
import { computed } from 'vue'
import { RouterLink } from 'vue-router'

const props = defineProps({
  type: {
    type: String,
    default: 'button',
    validator: value => ['button', 'submit', 'reset'].includes(value)
  },

  variant: {
    type: String,
    default: 'primary',
    validator: value => [
      'primary',
      'secondary',
      'outline',
      'ghost',
      'danger'
    ].includes(value)
  },

  size: {
    type: String,
    default: 'medium',
    validator: value => ['small', 'medium', 'large'].includes(value)
  },

  disabled: {
    type: Boolean,
    default: false
  },

  loading: {
    type: Boolean,
    default: false
  },

  fullWidth: {
    type: Boolean,
    default: false
  },

  as: {
    type: String,
    default: 'button',
    validator: value => ['button', 'a', 'router-link'].includes(value)
  },

  to: {
    type: [String, Object],
    default: undefined
  },

  href: {
    type: String,
    default: undefined
  },

  iconOnly: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['click'])

const isDisabled = computed(() => props.disabled || props.loading)

const componentTag = computed(() => {
  return props.as === 'router-link' ? RouterLink : props.as
})

const isButton = computed(() => props.as === 'button')

const componentProps = computed(() => {
  if (isButton.value) {
    return {
      type: props.type,
      disabled: isDisabled.value
    }
  }

  if (props.as === 'router-link') {
    return {
      to: props.to
    }
  }

  return {
    href: props.href
  }
})

function handleClick(event) {
  if (isDisabled.value) {
    event.preventDefault()
    event.stopPropagation()
    return
  }

  emit('click', event)
}
</script>

<template>
  <component
      :is="componentTag"
      v-bind="componentProps"
      class="app-button"
      :tabindex="!isButton && isDisabled ? -1 : undefined"
      :class="[
      `app-button--${variant}`,
      `app-button--${size}`,
      {
        'app-button--full-width': fullWidth,
        'app-button--icon-only': iconOnly,
        'app-button--disabled': isDisabled
      }
    ]"
      :aria-disabled="!isButton && isDisabled ? 'true' : undefined"
      :aria-busy="loading || undefined"
      @click="handleClick"
  >
    <span
      v-if="loading"
      class="app-button__spinner"
      aria-hidden="true"
    />

    <span
        v-if="$slots.icon"
        class="app-button__icon"
        :class="{ 'app-button__hidden': loading }"
        aria-hidden="true"
    >
      <slot name="icon" />
    </span>

    <span
        v-if="$slots.default && !iconOnly"
        class="app-button__content"
        :class="{ 'app-button__hidden': loading }"
    >
      <slot />
    </span>

    <template v-else>
      <span
          v-if="$slots.icon"
          class="app-button__icon"
          aria-hidden="true"
      >
        <slot name="icon" />
      </span>

      <span
          v-if="$slots.default && !iconOnly"
          class="app-button__content"
      >
        <slot />
      </span>
    </template>
  </component>
</template>

<style scoped lang="scss">
.app-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;

  position: relative;

  border: 1px solid transparent;
  border-radius: var(--button-radius, 8px);

  font-family: inherit;
  font-weight: 500;
  line-height: 1;

  cursor: pointer;
  user-select: none;

  text-decoration: none;
  vertical-align: middle;

  transition:
      background-color 0.2s ease,
      border-color 0.2s ease,
      color 0.2s ease,
      box-shadow 0.2s ease,
      transform 0.1s ease;

  &:active:not(:disabled):not(.app-button--disabled) {
    transform: translateY(1px);
  }

  &:focus-visible {
    outline: 2px solid var(--color-primary);
    outline-offset: 3px;
  }

  &:disabled,
  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  // Sizes

  &--small {
    min-height: 32px;
    padding: 6px 12px;
    font-size: 14px;
  }

  &--medium {
    min-height: 40px;
    padding: 10px 20px;
    font-size: 16px;
  }

  &--large {
    min-height: 48px;
    padding: 12px 26px;
    font-size: 18px;
  }

  // Variants

  &--primary {
    background-color: var(--color-primary);
    color: var(--color-on-primary, #fff);

    &:hover:not(:disabled):not(.app-button--disabled) {
      background-color: var(--color-primary-hover, var(--color-primary));
    }
  }

  &--secondary {
    background-color: var(--color-secondary, #e5e5e5);
    color: var(--color-on-secondary, #212121);

    &:hover:not(:disabled):not(.app-button--disabled) {
      background-color: var(--color-secondary-hover, #d5d5d5);
    }
  }

  &--outline {
    background-color: transparent;
    border-color: var(--color-primary);
    color: var(--color-primary);

    &:hover:not(:disabled):not(.app-button--disabled) {
      background-color: var(--color-primary);
      color: var(--color-on-primary, #fff);
    }
  }

  &--ghost {
    background-color: transparent;
    color: var(--color-text);

    &:hover:not(:disabled):not(.app-button--disabled) {
      background-color: var(--color-surface-hover, #f0f0f0);
    }
  }

  &--danger {
    background-color: var(--color-danger, #dc3545);
    color: #fff;

    &:hover:not(:disabled):not(.app-button--disabled) {
      background-color: var(--color-danger-hover, #bb2d3b);
    }
  }

  // Modifiers

  &--full-width {
    width: 100%;
  }

  &--icon-only {
    padding: 0;
    aspect-ratio: 1 / 1;

    &.app-button--small {
      width: 32px;
      height: 32px;
    }

    &.app-button--medium {
      width: 40px;
      height: 40px;
    }

    &.app-button--large {
      width: 48px;
      height: 48px;
    }

    .app-button__icon {
      width: 100%;
      height: 100%;
    }
  }

  // Elements

  &__icon {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;

    :deep(svg),
    :deep(img) {
      width: 20px;
      height: 20px;
    }
  }

  &__content {
    display: inline-flex;
    align-items: center;
    justify-content: center;
  }

  &__hidden {
    visibility: hidden;
  }

  &__spinner {
    position: absolute;
    width: 18px;
    height: 18px;

    border: 2px solid currentColor;
    border-right-color: transparent;
    border-radius: 50%;

    animation: app-button-spin 0.7s linear infinite;
  }
}

@keyframes app-button-spin {
  to {
    transform: rotate(360deg);
  }
}
</style>