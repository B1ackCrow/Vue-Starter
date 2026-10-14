<script setup>
import { ref } from 'vue'
import AppButton from '../components/ui/AppButton.vue'

const loading = ref(false)

function testLoading() {
  loading.value = true

  setTimeout(() => {
    loading.value = false
  }, 2000)
}
</script>

<template>
  <main class="button-demo">
    <h1>AppButton</h1>

    <div class="button-demo__row">
      <AppButton variant="primary">
        Primary
      </AppButton>

      <AppButton variant="secondary">
        Secondary
      </AppButton>

      <AppButton variant="outline">
        Outline
      </AppButton>

      <AppButton variant="ghost">
        Ghost
      </AppButton>

      <AppButton variant="danger">
        Danger
      </AppButton>

      <AppButton
          as="a"
          href="https://github.com"
          target="_blank"
          rel="noopener noreferrer"
      >
        GitHub
      </AppButton>

      <AppButton
          as="router-link"
          to="/profile"
          variant="outline"
      >
        Личный кабинет
      </AppButton>

      <AppButton
          icon-only
          variant="ghost"
          size="small"
          aria-label="Закрыть"
      >
        <template #icon>
          <span>✕</span>
        </template>
      </AppButton>

      <AppButton variant="primary">
        <template #icon>
          <span>+</span>
        </template>

        Добавить
      </AppButton>

      <AppButton
          as="router-link"
          to="/profile"
          disabled
      >
        Недоступно
      </AppButton>
    </div>

    <div class="button-demo__row">
      <AppButton size="small">Small</AppButton>
      <AppButton size="medium">Medium</AppButton>
      <AppButton size="large">Large</AppButton>
    </div>

    <div class="button-demo__row">
      <AppButton disabled>Disabled</AppButton>

      <AppButton :loading="loading" @click="testLoading">
        Test Loading
      </AppButton>
    </div>

    <AppButton full-width>
      Full Width
    </AppButton>
  </main>
</template>

<style scoped>
.button-demo {
  display: flex;
  flex-direction: column;
  gap: 24px;
  padding: 40px;
}

.button-demo__row {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 16px;
}
</style>