<script setup>
import { ref } from 'vue'
import AppInput from '../components/ui/AppInput.vue'

const name = ref('')
const email = ref('')
const password = ref('')
const search = ref('')
</script>

<template>
  <main class="demo">
    <h1>AppInput</h1>

    <AppInput
        v-model="name"
        label="Имя"
        placeholder="Введите имя"
        clearable
    />

    <AppInput
        v-model="email"
        type="email"
        label="Email"
        placeholder="example@mail.com"
        hint="Мы не передаём вашу почту третьим лицам"
    />

    <AppInput
        v-model="password"
        type="password"
        label="Пароль"
        placeholder="Введите пароль"
        show-password-toggle
        required
    />

    <AppInput
        v-model="search"
        type="search"
        placeholder="Поиск..."
        variant="filled"
        clearable
    />

    <AppInput
        label="Поле с ошибкой"
        error="Это поле обязательно для заполнения"
    />

    <AppInput
        label="Заблокированное поле"
        model-value="Недоступно для редактирования"
        disabled
    />
  </main>
</template>

<style scoped>
.demo {
  display: flex;
  flex-direction: column;
  gap: 20px;

  max-width: 500px;
  padding: 40px;
}
</style>