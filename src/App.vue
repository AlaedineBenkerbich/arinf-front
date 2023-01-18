<script setup>
import Pokedex from "@/components/Pokedex.vue";
</script>

<template>
  <header>
  </header>

  <main>
    <Pokedex />
  </main>
</template>

<style>
:root {
  /* https://developer.mozilla.org/en-US/docs/Web/CSS/var */
  --color-primary: #131921;
  --color-primary-text: white;

  --color-secondary: #232f3e;
  --color-secondary-text: white;

  --color-accent: #2a3f58;

  --color-deal: #cc0c39;
  --color-deal-complementary: white;

  --color-call-to-action: #ffd713;
  --color-call-to-action-complementary: black;

  --card-border: 1px solid #ddd;

  font-family: "Amazon Ember",Arial,sans-serif;
}
:root * {
  box-sizing: border-box;
}
* {
  margin: 0;
}
</style>
