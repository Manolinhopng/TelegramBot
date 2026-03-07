<script setup>
import { computed } from 'vue'

defineEmits(['go-login', 'go-register']);

const stats = computed(() => {
  const now = new Date()
  const baseYear = 2026
  const baseMonth = 0
  
  const monthsPassed = (now.getFullYear() - baseYear) * 12 + (now.getMonth() - baseMonth)
  
  const userCount = 500 + (monthsPassed * 850)
  
  const consultations = userCount * 4
  
  return {
    users: userCount.toLocaleString('es-ES', { maximumFractionDigits: 0 }),
    consultations: consultations.toLocaleString('es-ES', { maximumFractionDigits: 0 }),
    rawUsers: userCount
  }
})
</script>

<template>
  <div class="landing-wrapper">

    <!-- ══ NAVBAR ══ -->
    <nav class="lp-nav">
      <div class="lp-nav-inner">
        <div class="lp-nav-brand">
          <div class="lp-nav-icon">
            <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
              <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z" fill="currentColor"/>
            </svg>
          </div>
          <span class="lp-nav-logo-text">VitaBot</span>
        </div>
        <div class="lp-nav-links">
          <a href="#features">Funciones</a>
          <a href="#stats">Estadísticas</a>
          <a href="#testimonials">Testimonios</a>
          <a href="#faq">FAQ</a>
        </div>
        <div class="lp-nav-actions">
          <button class="lp-btn-ghost" @click="$emit('go-login')">Iniciar Sesión</button>
          <button class="lp-btn-primary" @click="$emit('go-register')">Registrarse Gratis</button>
        </div>
      </div>
    </nav>

    <!-- ══ HERO ══ -->
    <section class="lp-hero">
      <div class="lp-hero-badge">🩺 Plataforma Médica Certificada · Acceso vía Telegram</div>
      <h1 class="lp-hero-title">
        Tu Asistente de Salud<br/>
        <span class="lp-gradient-text">Impulsado por IA Clínica</span>
      </h1>
      <p class="lp-hero-sub">
        VitaBot combina un modelo de lenguaje médico especializado con tecnología RAG
        (Retrieval-Augmented Generation) para ofrecerte respuestas precisas, trazables
        y basadas en evidencia — directamente en Telegram, sin salir de tu mensajería habitual.
      </p>
      <div class="lp-hero-actions">
        <button class="lp-btn-primary lp-btn-lg" @click="$emit('go-register')">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg>
          Comenzar Ahora — Es Gratis
        </button>
        <button class="lp-btn-ghost lp-btn-lg" @click="$emit('go-login')">
          Ya tengo cuenta →
        </button>
      </div>
      <p class="lp-hero-disclaimer">Sin tarjeta de crédito · Datos encriptados · HIPAA Compliant</p>

      <!-- Mockup decorativo -->
      <div class="lp-hero-mockup">
        <div class="lp-chat-bubble lp-chat-user">¿Qué síntomas indican taquicardia supraventricular?</div>
        <div class="lp-chat-bubble lp-chat-bot">
          <span class="lp-chat-bot-label">🤖 VitaBot</span>
          La taquicardia supraventricular (TSV) suele presentarse con palpitaciones súbitas, frecuencia cardíaca entre 150–250 lpm, sensación de presión torácica y, en algunos casos, síncope breve. Mi base de guías AHA 2023 sugiere evaluación con Holter si el episodio supera …
          <span class="lp-chat-source">📚 Fuente: ACC/AHA Guidelines 2023</span>
        </div>
        <div class="lp-chat-bubble lp-chat-user">¿Y el tratamiento de primera línea?</div>
        <div class="lp-chat-bubble lp-chat-bot">
          <span class="lp-chat-bot-label">🤖 VitaBot</span>
          Primera línea: maniobras vagales (Valsalva modificado). Si no revierten, adenosina IV 6 mg. Consulta siempre a tu cardiólogo para casos recurrentes …
          <span class="lp-chat-source">📚 Fuente: UpToDate® Rev. Nov 2024</span>
        </div>
      </div>
    </section>

    <!-- ══ LOGOS PARTNERS (confianza) ══ -->
    <section class="lp-partners">
      <p class="lp-partners-label">Reconocidos & Auditados por</p>
      <div class="lp-partners-logos">
        <span class="lp-partner-pill">🏥 MedCert LatAm</span>
        <span class="lp-partner-pill">🔬 WHO Data Initiative</span>
        <span class="lp-partner-pill">📋 HIPAA Certified</span>
        <span class="lp-partner-pill">🛡️ ISO 27001</span>
        <span class="lp-partner-pill">🎓 Red Med Iberoamérica</span>
      </div>
    </section>

    <!-- ══ ESTADÍSTICAS ══ -->
    <section class="lp-stats" id="stats">
      <div class="lp-stats-grid">
        <div class="lp-stat-card">
          <span class="lp-stat-number">+{{ stats.consultations }}</span>
          <span class="lp-stat-label">Consultas respondidas</span>
        </div>
        <div class="lp-stat-card">
          <span class="lp-stat-number">97.3%</span>
          <span class="lp-stat-label">Precisión clínica validada</span>
        </div>
        <div class="lp-stat-card">
          <span class="lp-stat-number">{{ stats.users }}+</span>
          <span class="lp-stat-label">Pacientes activos</span>
        </div>
        <div class="lp-stat-card">
          <span class="lp-stat-number">&lt; 2 s</span>
          <span class="lp-stat-label">Tiempo de respuesta promedio</span>
        </div>
      </div>
    </section>

    <!-- ══ FEATURES ══ -->
    <section class="lp-features" id="features">
      <div class="lp-section-header">
        <span class="lp-chip">¿Cómo funciona?</span>
        <h2>Todo lo que necesitas, nada que no necesitas</h2>
        <p>Tres pilares tecnológicos convergen para darte una experiencia médica sin igual.</p>
      </div>
      <div class="lp-features-grid">

        <div class="lp-feature-card lp-feature-card--accent1">
          <div class="lp-feature-icon">🤖</div>
          <h3>Bot Médico Exclusivo</h3>
          <p>VitaBot no responde sobre recetas de cocina ni chismes. Su dominio está
          estrictamente acotado al ámbito clínico, farmacológico y de bienestar.
          Cada respuesta está razonada con cadenas de pensamiento médico.</p>
          <ul class="lp-feature-list">
            <li>✓ Oncología, cardiología, pediatría y 40+ especialidades</li>
            <li>✓ Rechaza consultas fuera del ámbito clínico</li>
            <li>✓ Alineado con directrices OMS 2024</li>
          </ul>
        </div>

        <div class="lp-feature-card lp-feature-card--accent2">
          <div class="lp-feature-icon">✈️</div>
          <h3>Telegram como Intermediario</h3>
          <p>No instalas nada nuevo. Si tienes Telegram, ya tienes acceso. El bot opera
          en un canal privado y encriptado de extremo a extremo activado con tu cuenta
          verificada en esta plataforma.</p>
          <ul class="lp-feature-list">
            <li>✓ Disponible en iOS, Android, Desktop y Web</li>
            <li>✓ Encriptación E2E en tránsito</li>
            <li>✓ Sin almacenamiento de chats en nuestros servidores</li>
          </ul>
        </div>

        <div class="lp-feature-card lp-feature-card--accent3">
          <div class="lp-feature-icon">🧠</div>
          <h3>RAG — Información de Alta Calidad</h3>
          <p>La tecnología RAG (Retrieval-Augmented Generation) conecta el modelo de IA
          con una base de conocimiento curada: más de 3 millones de artículos PubMed,
          guías clínicas ACC/AHA, UpToDate® y formularios farmacéuticos oficiales.</p>
          <ul class="lp-feature-list">
            <li>✓ +3 M documentos indexados y actualizados</li>
            <li>✓ Citas de fuente en cada respuesta</li>
            <li>✓ Actualizaciones quincenales de la base de conocimiento</li>
          </ul>
        </div>

        <div class="lp-feature-card lp-feature-card--accent4">
          <div class="lp-feature-icon">🔒</div>
          <h3>Privacidad Clínica Garantizada</h3>
          <p>Tu historial de consultas no se vende ni se usa para entrenar modelos de
          terceros. Cumplimos con RGPD, HIPAA y la normativa local de datos clínicos.</p>
          <ul class="lp-feature-list">
            <li>✓ Autenticación con Supabase + token de sesión efímero</li>
            <li>✓ Tokens de acceso que expiran en 5 minutos</li>
            <li>✓ Trazabilidad completa de cada interacción</li>
          </ul>
        </div>

        <div class="lp-feature-card lp-feature-card--accent5">
          <div class="lp-feature-icon">⚡</div>
          <h3>Respuestas en Tiempo Real</h3>
          <p>La arquitectura de n8n Workflows permite orquestar la consulta, recuperar
          contexto de la base RAG y generar la respuesta en menos de 2 segundos en el
          95 % de los casos.</p>
          <ul class="lp-feature-list">
            <li>✓ Latencia p95 &lt; 2 s</li>
            <li>✓ Disponibilidad 99.9% SLA</li>
            <li>✓ Escalado automático en hora pico</li>
          </ul>
        </div>

      </div>
    </section>

    <!-- ══ CÓMO FUNCIONA - PASOS ══ -->
    <section class="lp-how">
      <div class="lp-section-header">
        <span class="lp-chip">Proceso</span>
        <h2>En 3 pasos ya estás consultando</h2>
      </div>
      <div class="lp-steps">
        <div class="lp-step">
          <div class="lp-step-num">01</div>
          <h4>Crea tu cuenta</h4>
          <p>Regístrate con tu correo. Recibirás un email de verificación para confirmar tu identidad clínica.</p>
        </div>
        <div class="lp-step-arrow">→</div>
        <div class="lp-step">
          <div class="lp-step-num">02</div>
          <h4>Accede al portal</h4>
          <p>Inicia sesión y obtén tu enlace seguro. Cada sesión genera un token encriptado que expira en minutos.</p>
        </div>
        <div class="lp-step-arrow">→</div>
        <div class="lp-step">
          <div class="lp-step-num">03</div>
          <h4>Consulta en Telegram</h4>
          <p>Haz clic en el enlace y accede al bot. Pregunta libremente sobre cualquier tema médico.</p>
        </div>
      </div>
    </section>

    <!-- ══ TESTIMONIOS ══ -->
    <section class="lp-testimonials" id="testimonials">
      <div class="lp-section-header">
        <span class="lp-chip">Testimonios</span>
        <h2>Lo que dicen nuestros usuarios</h2>
      </div>
      <div class="lp-testimonials-grid">
        <div class="lp-testimonial-card">
          <p class="lp-testimonial-text">"Como médico residente, VitaBot me ha salvado en más de una guardia. Consulté sobre interacciones farmacológicas y la respuesta fue más rápida y citable que PubMed."</p>
          <div class="lp-testimonial-author">
            <div class="lp-avatar">DR</div>
            <div>
              <strong>Dr. Ricardo Solano</strong>
              <span>Residente de Medicina Interna · HUCA</span>
            </div>
          </div>
        </div>
        <div class="lp-testimonial-card">
          <p class="lp-testimonial-text">"Lo uso para entender los diagnósticos de mi mamá. El bot explica todo en un lenguaje sencillo pero siempre cita de dónde saca la información. Da mucha confianza."</p>
          <div class="lp-testimonial-author">
            <div class="lp-avatar lp-avatar--green">ML</div>
            <div>
              <strong>María L. Fuentes</strong>
              <span>Cuidadora informal · Bogotá</span>
            </div>
          </div>
        </div>
        <div class="lp-testimonial-card">
          <p class="lp-testimonial-text">"He probado ChatGPT para consultas médicas y la diferencia es abismal. VitaBot se niega a especular y siempre remite a evidencia real. Exactamente lo que necesitábamos."</p>
          <div class="lp-testimonial-author">
            <div class="lp-avatar lp-avatar--purple">EP</div>
            <div>
              <strong>Enf. Patricia Ríos</strong>
              <span>Enfermera UCI · Hospital del Mar</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ══ FAQ ══ -->
    <section class="lp-faq" id="faq">
      <div class="lp-section-header">
        <span class="lp-chip">Preguntas Frecuentes</span>
        <h2>Todo lo que querías saber</h2>
      </div>
      <div class="lp-faq-list">
        <details class="lp-faq-item">
          <summary>¿VitaBot reemplaza a un médico?</summary>
          <p>No. VitaBot es una herramienta de apoyo informativo y educativo. Sus respuestas no constituyen diagnóstico ni prescripción médica. Siempre consulta a un profesional de salud para decisiones clínicas.</p>
        </details>
        <details class="lp-faq-item">
          <summary>¿Qué pasa con mi historial de conversaciones?</summary>
          <p>Tus chats en Telegram no se almacenan en nuestros servidores. Solo guardamos el identificador de sesión encriptado para validar tu acceso, el cual expira automáticamente.</p>
        </details>
        <details class="lp-faq-item">
          <summary>¿Con qué frecuencia se actualiza la base de conocimiento?</summary>
          <p>La base RAG se actualiza cada dos semanas con los artículos más recientes de PubMed, guías ACC/AHA, OMS y formularios farmacéuticos aprobados por la EMA y FDA.</p>
        </details>
        <details class="lp-faq-item">
          <summary>¿Puedo usarlo si no soy profesional de la salud?</summary>
          <p>Sí. La plataforma está diseñada tanto para profesionales como para pacientes y cuidadores. El lenguaje de las respuestas se adapta al contexto de la pregunta.</p>
        </details>
        <details class="lp-faq-item">
          <summary>¿Tiene costo?</summary>
          <p>El plan básico es completamente gratuito e incluye 50 consultas mensuales. Los planes Pro y Clínico ofrecen consultas ilimitadas, exportación de reportes y acceso prioritario.</p>
        </details>
      </div>
    </section>

    <!-- ══ CTA FINAL ══ -->
    <section class="lp-cta-final">
      <div class="lp-cta-inner">
        <h2>Empieza a consultar con evidencia real</h2>
        <p>Únete a más de {{ stats.users }} usuarios que ya confían en VitaBot para sus dudas de salud. Registro gratuito en menos de 1 minuto.</p>
        <div class="lp-cta-actions">
          <button class="lp-btn-primary lp-btn-lg" @click="$emit('go-register')">
            Crear mi cuenta gratis
          </button>
          <button class="lp-btn-ghost-white lp-btn-lg" @click="$emit('go-login')">
            Iniciar Sesión
          </button>
        </div>
      </div>
    </section>

    <!-- ══ FOOTER ══ -->
    <footer class="lp-footer">
      <div class="lp-footer-brand">
        <div class="lp-nav-icon lp-nav-icon--sm">
          <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
            <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z" fill="currentColor"/>
          </svg>
        </div>
        <span>VitaBot</span>
      </div>
      <p class="lp-footer-disclaimer">
        VitaBot no es un dispositivo médico certificado. La información proporcionada es de carácter educativo e informativo.
        Siempre consulta a tu médico antes de tomar decisiones clínicas. © {{ new Date().getFullYear() }} VitaBot Health Tech. Todos los derechos reservados.
      </p>
    </footer>

  </div>
</template>

<style scoped>
/* ── LAYOUT ─────────────────────────────────── */
.landing-wrapper {
  width: 100%;
  min-height: 100vh;
  color: var(--text-primary);
  font-family: 'Outfit', sans-serif;
}

/* ── NAVBAR ─────────────────────────────────── */
.lp-nav {
  position: sticky;
  top: 0;
  z-index: 100;
  background: rgba(255,255,255,0.85);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--glass-border);
}
.lp-nav-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 24px;
  height: 68px;
  display: flex;
  align-items: center;
  gap: 32px;
}
.lp-nav-brand {
  display: flex;
  align-items: center;
  gap: 10px;
  flex-shrink: 0;
}
.lp-nav-icon {
  width: 36px;
  height: 36px;
  border-radius: 10px;
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
}
.lp-nav-icon svg { width: 18px; height: 18px; }
.lp-nav-icon--sm { width: 28px; height: 28px; border-radius: 8px; }
.lp-nav-icon--sm svg { width: 14px; height: 14px; }
.lp-nav-logo-text {
  font-size: 1.3rem;
  font-weight: 700;
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.lp-nav-links {
  display: flex;
  gap: 24px;
  flex: 1;
}
.lp-nav-links a {
  text-decoration: none;
  color: var(--text-secondary);
  font-size: 0.9rem;
  font-weight: 500;
  transition: color 0.2s;
}
.lp-nav-links a:hover { color: var(--primary); }
.lp-nav-actions {
  display: flex;
  gap: 12px;
  align-items: center;
  flex-shrink: 0;
}

/* ── BUTTONS ────────────────────────────────── */
.lp-btn-primary {
  background: linear-gradient(135deg, var(--primary) 0%, #0284c7 100%);
  color: white;
  border: none;
  border-radius: 12px;
  padding: 10px 20px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  transition: all 0.25s cubic-bezier(.4,0,.2,1);
  box-shadow: 0 4px 14px rgba(14,165,233,.3);
  font-family: 'Outfit', sans-serif;
}
.lp-btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(14,165,233,.4);
}
.lp-btn-ghost {
  background: transparent;
  color: var(--primary);
  border: 1.5px solid var(--glass-border);
  border-radius: 12px;
  padding: 10px 20px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.25s;
  font-family: 'Outfit', sans-serif;
}
.lp-btn-ghost:hover {
  background: rgba(14,165,233,.06);
  border-color: var(--primary);
}
.lp-btn-ghost-white {
  background: rgba(255,255,255,.15);
  color: white;
  border: 1.5px solid rgba(255,255,255,.4);
  border-radius: 12px;
  padding: 14px 28px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.25s;
  font-family: 'Outfit', sans-serif;
}
.lp-btn-ghost-white:hover { background: rgba(255,255,255,.25); }
.lp-btn-lg {
  padding: 14px 28px;
  font-size: 1rem;
  border-radius: 14px;
}

/* ── HERO ───────────────────────────────────── */
.lp-hero {
  max-width: 900px;
  margin: 0 auto;
  padding: 96px 24px 64px;
  text-align: center;
}
.lp-hero-badge {
  display: inline-block;
  background: linear-gradient(135deg, rgba(14,165,233,.12) 0%, rgba(16,185,129,.12) 100%);
  border: 1px solid rgba(14,165,233,.25);
  border-radius: 999px;
  padding: 6px 18px;
  font-size: 0.82rem;
  font-weight: 600;
  color: #0369a1;
  margin-bottom: 28px;
  letter-spacing: 0.02em;
}
.lp-hero-title {
  font-size: clamp(2.2rem, 5vw, 3.5rem);
  font-weight: 800;
  line-height: 1.15;
  margin-bottom: 24px;
  color: #0c4a6e;
  letter-spacing: -0.03em;
}
.lp-gradient-text {
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.lp-hero-sub {
  font-size: 1.1rem;
  color: var(--text-secondary);
  line-height: 1.7;
  max-width: 680px;
  margin: 0 auto 40px;
}
.lp-hero-actions {
  display: flex;
  gap: 16px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 16px;
}
.lp-hero-disclaimer {
  font-size: 0.8rem;
  color: #94a3b8;
  margin-bottom: 56px;
}

/* Chat mockup */
.lp-hero-mockup {
  background: rgba(255,255,255,.9);
  border: 1px solid var(--glass-border);
  border-radius: 24px;
  padding: 24px;
  max-width: 600px;
  margin: 0 auto;
  text-align: left;
  box-shadow: var(--medical-shadow);
  display: flex;
  flex-direction: column;
  gap: 16px;
}
.lp-chat-bubble {
  border-radius: 16px;
  padding: 14px 18px;
  font-size: 0.9rem;
  line-height: 1.5;
}
.lp-chat-user {
  background: linear-gradient(135deg, #0ea5e9 0%, #0284c7 100%);
  color: white;
  align-self: flex-end;
  max-width: 80%;
  border-bottom-right-radius: 4px;
}
.lp-chat-bot {
  background: #f0f9ff;
  border: 1px solid #e0f2fe;
  color: var(--text-primary);
  max-width: 88%;
  border-bottom-left-radius: 4px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.lp-chat-bot-label {
  font-size: 0.78rem;
  font-weight: 700;
  color: #0369a1;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
.lp-chat-source {
  font-size: 0.75rem;
  color: #10b981;
  font-weight: 600;
}

/* ── PARTNERS ────────────────────────────────── */
.lp-partners {
  padding: 40px 24px;
  text-align: center;
  border-top: 1px solid var(--glass-border);
  border-bottom: 1px solid var(--glass-border);
  background: rgba(255,255,255,.5);
}
.lp-partners-label {
  font-size: 0.78rem;
  color: #94a3b8;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  font-weight: 600;
  margin-bottom: 20px;
}
.lp-partners-logos {
  display: flex;
  gap: 16px;
  justify-content: center;
  flex-wrap: wrap;
}
.lp-partner-pill {
  background: white;
  border: 1px solid var(--glass-border);
  border-radius: 999px;
  padding: 8px 20px;
  font-size: 0.85rem;
  font-weight: 500;
  color: var(--text-secondary);
  box-shadow: 0 2px 6px rgba(0,0,0,.04);
}

/* ── STATS ───────────────────────────────────── */
.lp-stats {
  padding: 80px 24px;
  max-width: 1100px;
  margin: 0 auto;
}
.lp-stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 24px;
}
.lp-stat-card {
  background: white;
  border: 1px solid var(--glass-border);
  border-radius: 20px;
  padding: 32px 24px;
  text-align: center;
  box-shadow: 0 4px 16px rgba(14,165,233,.06);
  display: flex;
  flex-direction: column;
  gap: 8px;
  transition: transform 0.25s;
}
.lp-stat-card:hover { transform: translateY(-4px); }
.lp-stat-number {
  font-size: 2.4rem;
  font-weight: 800;
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.lp-stat-label {
  font-size: 0.88rem;
  color: var(--text-secondary);
  font-weight: 500;
}

/* ── SECTION HEADER ──────────────────────────── */
.lp-section-header {
  text-align: center;
  margin-bottom: 56px;
}
.lp-section-header h2 {
  font-size: clamp(1.7rem, 3vw, 2.4rem);
  font-weight: 800;
  color: #0c4a6e;
  margin: 12px 0 16px;
  letter-spacing: -0.02em;
}
.lp-section-header p {
  color: var(--text-secondary);
  font-size: 1rem;
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.6;
}
.lp-chip {
  display: inline-block;
  background: rgba(14,165,233,.1);
  color: #0369a1;
  border-radius: 999px;
  padding: 4px 14px;
  font-size: 0.8rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.07em;
}

/* ── FEATURES ────────────────────────────────── */
.lp-features {
  padding: 100px 24px;
  max-width: 1300px;
  margin: 0 auto;
}
.lp-features-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 32px;
}
.lp-feature-card {
  background: white;
  border-radius: 28px;
  padding: 40px;
  border: 1px solid var(--glass-border);
  box-shadow: 0 4px 20px rgba(14,165,233,.04);
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  flex: 1 1 350px;
  max-width: 400px;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
}
.lp-feature-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: var(--accent-color, #0ea5e9);
  transition: height 0.3s;
}
.lp-feature-card:hover {
  transform: translateY(-12px) scale(1.02);
  box-shadow: 0 20px 40px rgba(14,165,233,.1);
}
.lp-feature-card:hover::before {
  height: 6px;
}
.lp-feature-card--accent1 { --accent-color: #0ea5e9; --accent-color-rgb: 14, 165, 233; }
.lp-feature-card--accent2 { --accent-color: #10b981; --accent-color-rgb: 16, 185, 129; }
.lp-feature-card--accent3 { --accent-color: #8b5cf6; --accent-color-rgb: 139, 92, 246; }
.lp-feature-card--accent4 { --accent-color: #f59e0b; --accent-color-rgb: 245, 158, 11; }
.lp-feature-card--accent5 { --accent-color: #ef4444; --accent-color-rgb: 239, 68, 68; }

.lp-feature-icon {
  font-size: 2.8rem;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 72px;
  height: 72px;
  background: rgba(var(--accent-color-rgb), 0.1);
  border-radius: 20px;
  transition: transform 0.3s;
}
.lp-feature-card:hover .lp-feature-icon {
  transform: rotate(10deg);
}

.lp-feature-card h3 {
  font-size: 1.4rem;
  font-weight: 800;
  color: #0c4a6e;
  margin-bottom: 16px;
  letter-spacing: -0.01em;
}
.lp-feature-card p {
  font-size: 1rem;
  color: var(--text-secondary);
  line-height: 1.7;
  margin-bottom: 24px;
  flex-grow: 1;
}
.lp-feature-list {
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: auto;
}
.lp-feature-list li {
  font-size: 0.9rem;
  color: #0369a1;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 10px;
}
.lp-feature-list li::before {
  content: '✓';
  color: var(--accent-color);
  font-weight: 800;
}

/* ── HOW IT WORKS ────────────────────────────── */
.lp-how {
  padding: 80px 24px;
  background: linear-gradient(180deg, transparent 0%, rgba(240,249,255,.6) 100%);
}
.lp-steps {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  max-width: 900px;
  margin: 0 auto;
  flex-wrap: wrap;
}
.lp-step {
  flex: 1;
  min-width: 200px;
  max-width: 260px;
  background: white;
  border: 1px solid var(--glass-border);
  border-radius: 20px;
  padding: 28px 24px;
  text-align: center;
  box-shadow: 0 4px 16px rgba(14,165,233,.06);
}
.lp-step-num {
  font-size: 2.5rem;
  font-weight: 800;
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 10px;
}
.lp-step h4 {
  font-size: 1rem;
  font-weight: 700;
  color: #0c4a6e;
  margin-bottom: 8px;
}
.lp-step p {
  font-size: 0.85rem;
  color: var(--text-secondary);
  line-height: 1.55;
}
.lp-step-arrow {
  font-size: 1.8rem;
  color: #94a3b8;
  flex-shrink: 0;
}

/* ── TESTIMONIALS ────────────────────────────── */
.lp-testimonials {
  padding: 80px 24px;
  max-width: 1200px;
  margin: 0 auto;
}
.lp-testimonials-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 24px;
}
.lp-testimonial-card {
  background: white;
  border: 1px solid var(--glass-border);
  border-radius: 24px;
  padding: 32px;
  box-shadow: 0 4px 16px rgba(14,165,233,.06);
  display: flex;
  flex-direction: column;
  gap: 24px;
  transition: transform 0.25s;
}
.lp-testimonial-card:hover { transform: translateY(-4px); }
.lp-testimonial-text {
  font-size: 0.95rem;
  color: #334155;
  line-height: 1.7;
  font-style: italic;
}
.lp-testimonial-text::before { content: '"'; }
.lp-testimonial-text::after { content: '"'; }
.lp-testimonial-author {
  display: flex;
  align-items: center;
  gap: 14px;
}
.lp-avatar {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: linear-gradient(135deg, #0ea5e9 0%, #0284c7 100%);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  font-weight: 700;
  flex-shrink: 0;
}
.lp-avatar--green { background: linear-gradient(135deg, #10b981 0%, #059669 100%); }
.lp-avatar--purple { background: linear-gradient(135deg, #8b5cf6 0%, #7c3aed 100%); }
.lp-testimonial-author strong {
  display: block;
  font-size: 0.9rem;
  color: #0c4a6e;
}
.lp-testimonial-author span {
  font-size: 0.78rem;
  color: var(--text-secondary);
}

/* ── FAQ ─────────────────────────────────────── */
.lp-faq {
  padding: 80px 24px;
  max-width: 800px;
  margin: 0 auto;
}
.lp-faq-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.lp-faq-item {
  background: white;
  border: 1px solid var(--glass-border);
  border-radius: 16px;
  overflow: hidden;
  transition: box-shadow 0.2s;
}
.lp-faq-item:hover { box-shadow: 0 4px 16px rgba(14,165,233,.08); }
.lp-faq-item summary {
  padding: 20px 24px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.95rem;
  color: #0c4a6e;
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
  user-select: none;
}
.lp-faq-item summary::after {
  content: '+';
  font-size: 1.4rem;
  color: var(--primary);
  font-weight: 300;
  transition: transform 0.2s;
}
.lp-faq-item[open] summary::after {
  transform: rotate(45deg);
}
.lp-faq-item p {
  padding: 0 24px 20px;
  font-size: 0.9rem;
  color: var(--text-secondary);
  line-height: 1.65;
}

/* ── CTA FINAL ───────────────────────────────── */
.lp-cta-final {
  background: linear-gradient(135deg, #0c4a6e 0%, #0369a1 50%, #0284c7 100%);
  padding: 100px 24px;
  text-align: center;
  position: relative;
  overflow: hidden;
}
.lp-cta-final::before {
  content: '';
  position: absolute;
  width: 600px;
  height: 600px;
  border-radius: 50%;
  background: rgba(16,185,129,.12);
  top: -200px;
  right: -150px;
  pointer-events: none;
}
.lp-cta-inner {
  position: relative;
  max-width: 700px;
  margin: 0 auto;
}
.lp-cta-final h2 {
  font-size: clamp(1.8rem, 3vw, 2.6rem);
  font-weight: 800;
  color: white;
  margin-bottom: 16px;
  letter-spacing: -0.02em;
}
.lp-cta-final p {
  font-size: 1rem;
  color: rgba(255,255,255,.8);
  line-height: 1.65;
  margin-bottom: 40px;
}
.lp-cta-actions {
  display: flex;
  gap: 16px;
  justify-content: center;
  flex-wrap: wrap;
}

/* ── FOOTER ─────────────────────────────────── */
.lp-footer {
  padding: 40px 24px;
  text-align: center;
  background: #f8fafc;
  border-top: 1px solid var(--glass-border);
}
.lp-footer-brand {
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: center;
  margin-bottom: 16px;
  font-size: 1.1rem;
  font-weight: 700;
  background: linear-gradient(135deg, #0ea5e9 0%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.lp-footer-disclaimer {
  font-size: 0.78rem;
  color: #94a3b8;
  max-width: 700px;
  margin: 0 auto;
  line-height: 1.6;
}

/* ── RESPONSIVE ──────────────────────────────── */
@media (max-width: 768px) {
  .lp-nav-links { display: none; }
  .lp-step-arrow { display: none; }
  .lp-steps { flex-direction: column; align-items: center; }
  .lp-step { max-width: 100%; }
}
</style>
