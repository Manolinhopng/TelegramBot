<script setup>
import { ref, onMounted } from "vue";
import { supabase } from "./lib/supabase";
import Auth from "./components/Auth.vue";
import LandingPage from "./components/LandingPage.vue";

// 'landing' | 'login' | 'register' | 'reset'
const currentView = ref("landing");

const goLogin = () => { currentView.value = "login"; };
const goRegister = () => { currentView.value = "register"; };
const goLanding = () => { currentView.value = "landing"; };

onMounted(() => {
  supabase.auth.onAuthStateChange((event, session) => {
    if (event === "PASSWORD_RECOVERY") {
      currentView.value = "reset";
    }
  });
});
</script>

<template>
  <LandingPage
    v-if="currentView === 'landing'"
    @go-login="goLogin"
    @go-register="goRegister"
  />
  <div v-else class="auth-page-layout">
    <Auth
      :initial-mode="currentView === 'register' ? 'register' : currentView === 'reset' ? 'reset' : 'login'"
      @go-landing="goLanding"
    />
  </div>
</template>

<style>
/* Global styles are in style.css */
</style>
