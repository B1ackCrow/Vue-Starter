<script setup>
import { computed } from 'vue'

const props = defineProps({
  image: {
    type: String,
    default: ''
  },

  imageAlt: {
    type: String,
    default: ''
  },

  title: {
    type: String,
    default: ''
  },

  subtitle: {
    type: String,
    default: ''
  },

  variant: {
    type: String,
    default: 'default',
    validator: value => [
      'default',
      'outlined',
      'elevated'
    ].includes(value)
  },

  size: {
    type: String,
    default: 'medium',
    validator: value => [
      'small',
      'medium',
      'large'
    ].includes(value)
  },

  clickable: {
    type: Boolean,
    default: false
  },

  loading: {
    type: Boolean,
    default: false
  },

  disabled: {
    type: Boolean,
    default: false
  },

  imageRatio: {
    type: String,
    default: '4 / 3'
  }
})

const emit = defineEmits(['click'])

const isInteractive = computed(() => {
  return props.clickable && !props.loading && !props.disabled
})

function handleClick(event) {
  if (!isInteractive.value) return

  emit('click', event)
}

function handleKeydown(event) {
  if (!isInteractive.value) return

  if (event.key === 'Enter' || event.key === ' ') {
    event.preventDefault()
    emit('click', event)
  }
}
</script>

<template>
  <article
      class="app-card"
      :class="[
      `app-card--${variant}`,
      `app-card--${size}`,
      {
        'app-card--clickable': isInteractive,
        'app-card--loading': loading,
        'app-card--disabled': disabled
      }
    ]"
      :style="{ '--card-image-ratio': imageRatio }"
      :role="clickable ? 'button' : undefined"
      :tabindex="isInteractive ? 0 : undefined"
      :aria-disabled="clickable && (disabled || loading) ? 'true' : undefined"
      :aria-busy="loading || undefined"
      @click="handleClick"
      @keydown.self="handleKeydown"
  >
    <div
        v-if="image || $slots.image"
        class="app-card__image"
    >
      <slot name="image">
        <img
            :src="image"
            :alt="imageAlt"
            loading="lazy"
        />
      </slot>
    </div>

    <div class="app-card__body">
      <div
          v-if="title || $slots.header"
          class="app-card__header"
      >
        <slot name="header">
          <h3 class="app-card__title">
            {{ title }}
          </h3>
        </slot>
      </div>

      <p
          v-if="subtitle"
          class="app-card__subtitle"
      >
        {{ subtitle }}
      </p>

      <div
          v-if="$slots.default"
          class="app-card__content"
      >
        <slot />
      </div>

      <div
          v-if="$slots.footer"
          class="app-card__footer"
      >
        <slot name="footer" />
      </div>
    </div>
  </article>
</template>

<style scoped lang="scss">
.app-card {
  display: flex;
  flex-direction: column;

  width: 100%;
  min-width: 0;

  overflow: hidden;

  border-radius: var(--card-radius, 12px);
  background-color: var(--card-background, #fff);
  color: var(--color-text);

  transition:
      transform 0.2s ease,
      box-shadow 0.2s ease,
      border-color 0.2s ease,
      opacity 0.2s ease;

  // Variants

  &--default {
    border: 1px solid transparent;
  }

  &--outlined {
    border: 1px solid var(--card-border, #e5e5e5);
  }

  &--elevated {
    border: 1px solid transparent;
    box-shadow: var(
            --card-shadow,
            0 4px 16px rgba(0, 0, 0, 0.08)
    );
  }

  // Sizes

  &--small {
    --card-padding: 12px;
    --card-title-size: 14px;
  }

  &--medium {
    --card-padding: 16px;
    --card-title-size: 16px;
  }

  &--large {
    --card-padding: 24px;
    --card-title-size: 20px;
  }

  // States

  &--clickable {
    cursor: pointer;

    &:hover {
      transform: translateY(-3px);

      box-shadow: var(
              --card-hover-shadow,
              0 8px 24px rgba(0, 0, 0, 0.12)
      );
    }

    &:focus-visible {
      outline: 2px solid var(--color-primary);
      outline-offset: 3px;
    }
  }

  &--loading {
    opacity: 0.6;
    pointer-events: none;
  }

  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  // Image

  &__image {
    width: 100%;
    aspect-ratio: var(--card-image-ratio, 4 / 3);

    overflow: hidden;

    background-color: var(
            --card-image-background,
            #f5f5f5
    );

    :deep(img) {
      display: block;

      width: 100%;
      height: 100%;

      object-fit: cover;

      transition: transform 0.3s ease;
    }
  }

  &--clickable:hover &__image :deep(img) {
    transform: scale(1.04);
  }

  // Body

  &__body {
    display: flex;
    flex-direction: column;
    flex: 1;

    gap: 10px;
    padding: var(--card-padding);
  }

  &__title {
    margin: 0;

    font-size: var(--card-title-size);
    font-weight: 600;
    line-height: 1.4;
  }

  &__subtitle {
    margin: 0;

    font-size: 13px;
    color: var(--color-text-secondary, #777);
  }

  &__content {
    flex: 1;

    font-size: 14px;
    line-height: 1.6;
  }

  &__footer {
    margin-top: auto;
    padding-top: 8px;
  }
}
</style>