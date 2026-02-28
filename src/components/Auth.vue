<script setup>
import { ref } from "vue";
import { supabase } from "../lib/supabase";

const loading = ref(false);
const success = ref(false);
const email = ref("");
const password = ref("");
const isRegister = ref(false);
const errorMsg = ref("");
const telegramUrl = ref("");
const telegramWebUrl = ref("");

const handleAuth = async () => {
  try {
    loading.value = true;
    errorMsg.value = "";

    if (password.value.length < 6) {
      throw new Error("La contraseña debe tener al menos 6 caracteres.");
    }

    if (isRegister.value) {
      const { error } = await supabase.auth.signUp({
        email: email.value,
        password: password.value,
      });
      if (error) {
        if (error.message.includes("at least 6 characters")) {
          throw new Error(
            "La contraseña es demasiado corta (mínimo 6 caracteres).",
          );
        }
        throw error;
      }
      alert("Registro Clínico exitoso. Revisa tu email para confirmar.");
    } else {
      const { data, error } = await supabase.auth.signInWithPassword({
        email: email.value,
        password: password.value,
      });

      if (error) {
        if (error.message.includes("Invalid login credentials")) {
          throw new Error("Correo o contraseña incorrectos.");
        }
        if (error.message.includes("Email not confirmed")) {
          throw new Error(
            "Por favor, confirma tu correo electrónico antes de entrar.",
          );
        }
        throw error;
      }

      const userId = data.user.id;
      const exp = Math.floor((Date.now() + 5 * 60 * 1000) / 1000);
      const rawToken = `${userId}:${exp}`;
      const safeToken = btoa(rawToken)
        .replace(/\+/g, "-")
        .replace(/\//g, "_")
        .replace(/=+$/, "");

      telegramUrl.value = `https://t.me/tempPruebaBot?start=${safeToken}`;
      telegramWebUrl.value = `https://web.telegram.org/k/#@tempPruebaBot?start=${safeToken}`;
      success.value = true;
    }
  } catch (error) {
    errorMsg.value = error.message;
  } finally {
    loading.value = false;
  }
};

const toggleMode = () => {
  isRegister.value = !isRegister.value;
  errorMsg.value = "";
};
</script>

<template>
  <div class="auth-container">
    <div class="savia-logo">
      <div class="savia-logo-icon">
        <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
          <path
            d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
            fill="currentColor"
          />
        </svg>
      </div>
      <span class="savia-logo-text">VitaBot</span>
    </div>

    <div v-if="success" class="success-screen">
      <div class="success-icon">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="48"
          height="48"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2.5"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14" />
          <polyline points="22 4 12 14.01 9 11.01" />
        </svg>
      </div>
      <h2>Acceso Concedido</h2>
      <p>Identidad verificada. Seleccione cómo prefiere abrir el asistente.</p>
      <a :href="telegramUrl" target="_blank" rel="noopener" class="btn">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="18"
          height="18"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <line x1="22" y1="2" x2="11" y2="13" />
          <polygon points="22 2 15 22 11 13 2 9 22 2" />
        </svg>
        <span>Abrir App Telegram</span>
      </a>
      <a
        :href="telegramWebUrl"
        target="_blank"
        rel="noopener"
        class="btn btn-outline"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="18"
          height="18"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <circle cx="12" cy="12" r="10" />
          <line x1="2" y1="12" x2="22" y2="12" />
          <path
            d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"
          />
        </svg>
        <span>Abrir Telegram Web</span>
      </a>
      <p class="hint">
        "App Telegram" requiere tener Telegram Desktop instalado.
      </p>
    </div>

    <div v-else>
      <h1>{{ isRegister ? "Registro de Paciente" : "Portal Médico" }}</h1>
      <p class="subtitle">
        {{
          isRegister
            ? "Crea tu perfil clínico para acceder al asistente de salud en Telegram."
            : "Identifícate para sincronizar tus datos con el bot de Telegram."
        }}
      </p>

      <div v-if="errorMsg" class="error-msg">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="18"
          height="18"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <circle cx="12" cy="12" r="10" />
          <line x1="12" y1="8" x2="12" y2="12" />
          <line x1="12" y1="16" x2="12.01" y2="16" />
        </svg>
        {{ errorMsg }}
      </div>

      <form @submit.prevent="handleAuth">
        <div class="form-group">
          <label>Correo Electrónico</label>
          <input
            v-model="email"
            type="email"
            placeholder="nombre@ejemplo.com"
            required
          />
        </div>

        <div class="form-group">
          <label>Contraseña de Acceso</label>
          <input
            v-model="password"
            type="password"
            placeholder="Mínimo 6 caracteres"
            required
          />
        </div>

        <button class="btn" :disabled="loading">
          <div v-if="loading" class="loader"></div>
          <span v-else>{{
            isRegister ? "Confirmar Registro" : "Acceder al Portal"
          }}</span>
          <svg
            v-if="!loading"
            xmlns="http://www.w3.org/2000/svg"
            width="18"
            height="18"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path
              d="M11 2a2 2 0 0 0-2 2v5H4a2 2 0 0 0-2 2v2c0 1.1.9 2 2 2h5v5c0 1.1.9 2 2 2h2a2 2 0 0 0 2-2v-5h5a2 2 0 0 0 2-2v-2a2 2 0 0 0-2-2h-5V4a2 2 0 0 0-2-2h-2z"
            />
          </svg>
        </button>
      </form>

      <p class="toggle-mode">
        {{ isRegister ? "¿Ya eres usuario?" : "¿Nuevo paciente?" }}
        <span @click="toggleMode">{{
          isRegister ? "Iniciar Sesión" : "Registrarse ahora"
        }}</span>
      </p>
    </div>
  </div>
</template>
