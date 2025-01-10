<script setup>
  const generateEventId = () => {
    return Date.now();
  }
</script>

<template>
  <main>
    <header>
      <h1>Initiativ</h1>
    </header>

    <RouterLink :to="`/event/${generateEventId()}`" class="button">
      Create event
    </RouterLink>
  </main>
</template>

<style scoped>

header {
  padding: 1rem;
  background-color: var(--color-background-soft);
}

h1 {
  color: var(--color-heading);
}
</style>