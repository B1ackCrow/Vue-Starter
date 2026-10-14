<script setup>
import AppCard from '../components/ui/AppCard.vue'

const products = [
  {
    id: 1,
    name: 'Пицца Маргарита',
    category: 'Пицца',
    description: 'Томатный соус, моцарелла, свежий базилик.',
    price: 890,
    image: 'https://images.unsplash.com/photo-1574071318508-1cdbab80d002?w=800'
  },
  {
    id: 2,
    name: 'Паста Карбонара',
    category: 'Паста',
    description: 'Спагетти, бекон, пармезан и сливочный соус.',
    price: 750,
    image: 'https://images.unsplash.com/photo-1612874742237-6526221588e3?w=800'
  },
  {
    id: 3,
    name: 'Стейк Рибай',
    category: 'Основные блюда',
    description: 'Мраморная говядина, специи и ароматные травы.',
    price: 2450,
    image: 'https://images.unsplash.com/photo-1546833999-b9f581a1996d?w=800'
  }
]

function openProduct(product) {
  console.log('Выбран товар:', product)
}
</script>

<template>
  <main class="demo">
    <h1>AppCard</h1>

    <div class="demo__grid">
      <AppCard
          v-for="product in products"
          :key="product.id"
          :image="product.image"
          :image-alt="product.name"
          :title="product.name"
          :subtitle="product.category"
          variant="elevated"
          clickable
          @click="openProduct(product)"
      >
        <p>{{ product.description }}</p>

        <template #footer>
          <strong>{{ product.price }} ₽</strong>
        </template>
      </AppCard>
    </div>
  </main>
</template>

<style scoped lang="scss">
.demo {
  padding: 40px;

  h1 {
    margin-bottom: 32px;
  }

  &__grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 24px;

    max-width: 1200px;
  }
}

@media (max-width: 900px) {
  .demo__grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 600px) {
  .demo {
    padding: 20px;
  }

  .demo__grid {
    grid-template-columns: 1fr;
  }
}
</style>