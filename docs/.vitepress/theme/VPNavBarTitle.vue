<template>
  <div class="aee-nav-container" v-if="hasSidebar">
    <div>
      <div class="aee-nav-hero">
        AEE
      </div>
      <span class="nav-sub">Agentic Engineering Essentials</span>
    </div>
  </div>
</template>

<script setup>
import { useSidebar } from 'vitepress/theme';
const { hasSidebar } = useSidebar();
</script>

<style>
.aee-nav-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  padding: 15px 0;
}
.aee-nav-hero {
  font-size: 20px;
  background: -webkit-linear-gradient(left, #6d28d9, white, #7c3aed);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  font-weight: bold;
}
span.nav-sub {
  padding: 10px;
}
</style>
