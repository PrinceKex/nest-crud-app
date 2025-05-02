<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <RouterView />
</template>

<style>
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --background-color: #f4f4f4;
  --text-color: #333;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  line-height: 1.6;
  background-color: var(--background-color);
  color: var(--text-color);
}

#app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 1rem;
}

.auth-container, .home-container {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  max-width: 500px;
  margin: 2rem auto;
}

input, button {
  width: 100%;
  padding: 0.75rem;
  margin-bottom: 1rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: darken(var(--primary-color), 10%);
}

.error-message {
  color: #e74c3c;
  margin-top: 1rem;
}

@media (max-width: 1023px) {
  #app {
    padding: 0.5rem;
    max-width: 100%;
  }

  .auth-container, .home-container {
    width: 95%;
    max-width: 95%;
    padding: 1rem;
  }
}
</style>
