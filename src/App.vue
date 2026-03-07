<script setup>
import { ref } from "vue";
import Auth from "./components/Auth.vue";
import LandingPage from "./components/LandingPage.vue";

// 'landing' | 'login' | 'register'
const currentView = ref("landing");

const goLogin = () => { currentView.value = "login"; };
const goRegister = () => { currentView.value = "register"; };
const goLanding = () => { currentView.value = "landing"; };
</script>

<template>
  <LandingPage
    v-if="currentView === 'landing'"
    @go-login="goLogin"
    @go-register="goRegister"
  />
  <div v-else class="auth-page-layout">
    <Auth
      :initial-mode="currentView === 'register' ? 'register' : 'login'"
      @go-landing="goLanding"
    />
  </div>
</template>

<style>
/* Global styles are in style.css */
</style>
