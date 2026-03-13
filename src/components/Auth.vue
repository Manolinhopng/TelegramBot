<script setup>
import { ref, computed } from "vue";
import { supabase } from "../lib/supabase";

const props = defineProps({
  initialMode: { type: String, default: 'login' },
});
const emit = defineEmits(['go-landing']);

const loading = ref(false);
const success = ref(false);
const email = ref("");
const password = ref("");
const mode = ref(props.initialMode || "login"); // login, register, forgot, resend
const errorMsg = ref("");
const telegramUrl = ref("");
const telegramWebUrl = ref("");
const resendSuccess = ref("");
const showResend = ref(false);
const honeypot = ref("");

// --- Resend cooldown ---
const resendCooldown = ref(0);
let resendTimer = null;
let navigationTimeout = null;

const startResendCooldown = () => {
  resendCooldown.value = 60;
  if (resendTimer) clearInterval(resendTimer);
  resendTimer = setInterval(() => {
    resendCooldown.value--;
    if (resendCooldown.value <= 0) {
      clearInterval(resendTimer);
      resendTimer = null;
    }
  }, 1000);
};

const resendButtonLabel = computed(() => {
  if (loading.value) return null; // spinner shown instead
  if (resendCooldown.value > 0) return `Espera ${resendCooldown.value}s…`;
  return "Reenviar Correo";
});

// --- Email validation ---
const EMAIL_RE = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
const validateEmail = (val) => {
  if (!val || !EMAIL_RE.test(val)) {
    throw new Error("Por favor, ingresa un correo electrónico válido.");
  }
};

// --- Secure redirectTo for password reset ---
const getSecureOrigin = () => {
  const { protocol, hostname, port } = window.location;
  // Allow http only for local development
  const isLocal = hostname === "localhost" || hostname === "127.0.0.1";
  const useHttps = !isLocal;
  const safeProtocol = useHttps ? "https:" : protocol;
  const portPart = port ? `:${port}` : "";
  return `${safeProtocol}//${hostname}${portPart}`;
};

// --- Telegram deep-link token ---
// ⚠️ SECURITY NOTE: This token is base64(userId:expiry) — it is NOT cryptographically
// signed and can be decoded by anyone who sees the URL. It is short-lived (5 min) which
// limits exposure, but a proper fix requires a server-side HMAC/JWT (Supabase Edge Function).
// Upgrade when moving to production with real patient data.
const TELEGRAM_BOT = import.meta.env.VITE_TELEGRAM_BOT_USERNAME;

const generateTelegramLinks = (userId) => {
  const exp = Math.floor((Date.now() + 5 * 60 * 1000) / 1000);
  const rawToken = `${userId}:${exp}`;
  const safeToken = btoa(rawToken)
    .replace(/\+/g, "-")
    .replace(/\//g, "_")
    .replace(/=+$/, "");

  telegramUrl.value = `https://t.me/${TELEGRAM_BOT}?start=${safeToken}`;
  telegramWebUrl.value = `https://web.telegram.org/k/#@${TELEGRAM_BOT}?start=${safeToken}`;
};

const handleAuth = async () => {
  // Fix E: honeypot check BEFORE loading state so the spinner never appears for bots
  if (honeypot.value) return;
  if (loading.value) return;

  try {
    loading.value = true;
    // Anti-bot artificial delay (0.5s)
    await new Promise(resolve => setTimeout(resolve, 500));

    errorMsg.value = "";
    resendSuccess.value = "";
    if (navigationTimeout) clearTimeout(navigationTimeout);

    // Fix D: validate email format before hitting the API
    validateEmail(email.value);

    if (mode.value === "forgot") {
      await handleForgotPassword();
      return;
    }

    if (mode.value === "resend") {
      await handleResendEmail();
      return;
    }

    if (password.value.length < 6) {
      throw new Error("La contraseña debe tener al menos 6 caracteres.");
    }

    if (mode.value === "register") {
      const { error } = await supabase.auth.signUp({
        email: email.value,
        password: password.value,
      });
      if (error) {
        if (error.message.includes("at least 6 characters")) {
          throw new Error("La contraseña es demasiado corta (mínimo 6 caracteres).");
        }
        throw error;
      }
      resendSuccess.value = "Registro Clínico exitoso. Revisa tu email para confirmar tu cuenta.";
    } else {
      const { data, error } = await supabase.auth.signInWithPassword({
        email: email.value,
        password: password.value,
      });

      if (error) {
        if (error.message.toLowerCase().includes("invalid login credentials")) {
          throw new Error("Correo o contraseña incorrectos.");
        }
        if (error.message.toLowerCase().includes("email not confirmed")) {
          showResend.value = true;
          throw new Error(
            "Por favor, confirma tu correo electrónico antes de entrar.",
          );
        }
        throw error;
      }
      generateTelegramLinks(data.user.id);
      success.value = true;
    }
  } catch (error) {
    errorMsg.value = error.message;
  } finally {
    loading.value = false;
  }
};

const goBack = () => {
  if (navigationTimeout) clearTimeout(navigationTimeout);
  success.value = false;
  setMode("login");
};

const setMode = (newMode) => {
  mode.value = newMode;
  errorMsg.value = "";
  showResend.value = false;
  resendSuccess.value = "";
};

// Fix B: handleResendEmail no longer manages loading.value itself —
// it is always called from handleAuth which owns the loading state via finally.
// The early-return for already-authenticated users now simply sets the state and
// throws to let the caller's finally block clean up loading.
const handleResendEmail = async () => {
  errorMsg.value = "";
  resendSuccess.value = "";

  // Check cooldown
  if (resendCooldown.value > 0) {
    throw new Error(`Por favor espera ${resendCooldown.value} segundos antes de volver a enviar.`);
  }

  // Check for active session with same email
  const { data: { session } } = await supabase.auth.getSession();
  if (session && session.user.email === email.value) {
    generateTelegramLinks(session.user.id);
    resendSuccess.value = "¡Ya estás autenticado con este correo!";
    // Small delay so the user reads the message, then show success screen
    // We set success here and throw a sentinel so the caller finally() still runs
    await new Promise(resolve => setTimeout(resolve, 1000));
    success.value = true;
    return;
  }

  const { error } = await supabase.auth.resend({
    type: "signup",
    email: email.value,
  });

  if (error) {
    if (error.message.includes("already confirmed")) {
      resendSuccess.value = "Este correo ya ha sido confirmado. Puedes iniciar sesión.";
    } else if (
      error.message.toLowerCase().includes("rate limit") ||
      error.message.toLowerCase().includes("too many") ||
      error.status === 429
    ) {
      throw new Error("Demasiados intentos. Espera unos minutos antes de volver a enviar el correo.");
    } else {
      throw error;
    }
  } else {
    resendSuccess.value = "Correo de confirmación enviado. Revisa tu bandeja de entrada (y la carpeta de spam).";
    startResendCooldown();
  }

  showResend.value = false;
};

// Fix C: use getSecureOrigin() to ensure https: on production
const handleForgotPassword = async () => {
  errorMsg.value = "";
  resendSuccess.value = "";

  const { error } = await supabase.auth.resetPasswordForEmail(email.value, {
    redirectTo: getSecureOrigin(),
  });
  if (error) throw error;
  resendSuccess.value = "Se ha enviado un enlace para restablecer tu contraseña a tu correo.";
};
</script>

<template>
  <div class="auth-container">
    <!-- Back to landing -->
    <button class="auth-back-btn" @click="emit('go-landing')">
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"/></svg>
      Volver al inicio
    </button>

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
      <button @click="goBack" class="btn btn-outline" style="margin-top: 24px;">
        Volver al Inicio
      </button>
    </div>

    <div v-else>
      <h1>
        {{
          mode === "register"
            ? "Registro de Paciente"
            : mode === "forgot"
              ? "Recuperar Contraseña"
              : mode === "resend"
                ? "Reenviar Verificación"
                : "Portal Médico"
        }}
      </h1>
      <p class="subtitle">
        {{
          mode === "register"
            ? "Crea tu perfil clínico para acceder al asistente de salud en Telegram."
            : mode === "forgot"
              ? "Te enviaremos un enlace para restablecer tu contraseña."
              : mode === "resend"
                ? "Te enviaremos un nuevo correo de confirmación. Revisa también la carpeta de spam."
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
        <div v-if="showResend" class="resend-container">
          <button
            type="button"
            @click="setMode('resend')"
            class="btn-link"
          >
            Ir a Reenviar correo
          </button>
        </div>
      </div>

      <div v-if="resendSuccess" class="success-msg">
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
          <polyline points="20 6 9 17 4 12" />
        </svg>
        {{ resendSuccess }}
      </div>

      <form @submit.prevent="handleAuth">
        <!-- Anti-bot Honeypot Field -->
        <div style="display:none" aria-hidden="true">
          <input v-model="honeypot" type="text" name="hp_phone_number" tabindex="-1" autocomplete="off" />
        </div>

        <div class="form-group">
          <label>Correo Electrónico</label>
          <input
            v-model="email"
            type="email"
            placeholder="nombre@ejemplo.com"
            required
          />
        </div>

        <div v-if="mode === 'login' || mode === 'register'" class="form-group">
          <label>Contraseña de Acceso</label>
          <input
            v-model="password"
            type="password"
            placeholder="Mínimo 6 caracteres"
            required
          />
        </div>

        <button
          class="btn"
          :disabled="loading || (mode === 'resend' && resendCooldown > 0)"
        >
          <div v-if="loading" class="loader"></div>
          <span v-else>{{
            mode === "register"
              ? "Confirmar Registro"
              : mode === "forgot"
                ? "Enviar Enlace"
                : mode === "resend"
                  ? resendButtonLabel
                  : "Acceder al Portal"
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

      <p v-if="mode === 'login' || mode === 'register'" class="toggle-mode">
        {{ mode === "register" ? "¿Ya eres usuario?" : "¿Nuevo paciente?" }}
        <span @click="setMode(mode === 'register' ? 'login' : 'register')">{{
          mode === "register" ? "Iniciar Sesión" : "Registrarse ahora"
        }}</span>
      </p>

      <!-- Login: both forgot password AND resend verification visible -->
      <div v-if="mode === 'login'" class="navigation-links">
        <p class="toggle-mode small">
          <span @click="setMode('forgot')">¿Olvidaste tu contraseña?</span>
        </p>
        <p class="toggle-mode small">
          ¿No recibiste el correo de confirmación?
          <span @click="setMode('resend')">Reenviar verificación</span>
        </p>
      </div>

      <!-- Register: resend link -->
      <div v-if="mode === 'register'" class="navigation-links">
        <p class="toggle-mode small">
          ¿No recibiste el correo?
          <span @click="setMode('resend')">Reenviar verificación</span>
        </p>
      </div>

      <p v-if="mode === 'forgot' || mode === 'resend'" class="toggle-mode">
        O regresa al
        <span @click="setMode('login')">Inicio de Sesión</span>
      </p>
    </div>
  </div>
</template>
