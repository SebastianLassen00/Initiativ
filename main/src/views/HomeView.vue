<script setup>
import { ref as dbRef, set } from 'firebase/database'
import { db } from '@/data/config'
import { useRouter } from 'vue-router'
import CreateEventButton from '@/components/CreateEventButton.vue';

const router = useRouter()

</script>

<template>
  <main>
    <header>
      <h1>initiativ</h1>
    </header>

    <CreateEventButton/>
  </main>
</template>

<style scoped>
header {
  padding: 2rem;
  text-align: center;
}

.title {
  color: var(--color-heading);
  font-family: 'Playfair Display', 'Times New Roman', serif;
  font-style: italic;
  font-size: 3.5rem;
  font-weight: 400;
  margin: 0;
  letter-spacing: 0.05em;
}
</style>