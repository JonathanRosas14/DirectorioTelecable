<template>
  <div class="login-body">
    <div class="bg-decor bg-decor-1"></div>
    <div class="bg-decor bg-decor-2"></div>

    <div class="login-card">
      <div class="login-side">
        <div class="side-grid"></div>
        <div class="side-content">
          <h1 class="side-title">Bienvenido al <br />Directorio Interno</h1>
          <p class="side-desc">
            Accede a toda la informaci&oacute;n y detalles que necesitas de la empresa.
          </p>
          <div class="side-features">
            <div class="side-feature">
              <div class="feat-icon">
                <span class="material-symbols-outlined">domain</span>
              </div>
              <span>Oficinas y puntos de venta</span>
            </div>
            <div class="side-feature">
              <div class="feat-icon">
                <span class="material-symbols-outlined">call</span>
              </div>
              <span>Directorio de extensiones PBX</span>
            </div>
            <div class="side-feature">
              <div class="feat-icon">
                <span class="material-symbols-outlined">description</span>
              </div>
              <span>Formularios de ventas</span>
            </div>
            <div class="side-feature">
              <div class="feat-icon">
                <span class="material-symbols-outlined">payments</span>
              </div>
              <span>Informaci&oacute;n bancaria</span>
            </div>
          </div>
        </div>
      </div>

      <div class="login-form">
        <div class="form-inner">
          <div class="form-header">
            <div class="logo-wrap">
              <img src="../assets/Logo.png" alt="Telecable" />
            </div>
            <h2 class="form-title">Iniciar Sesi&oacute;n</h2>
            <p class="form-subtitle">Ingresa tus credenciales para continuar</p>
          </div>

          <div v-if="errorMessage" class="error-message" role="alert">
            <span class="material-symbols-outlined">warning</span>
            <span>{{ errorMessage }}</span>
          </div>

          <form @submit.prevent="handleLogin" novalidate>
            <div class="field">
              <label for="username">Correo Electr&oacute;nico</label>
              <div class="input-wrap">
                <span class="material-symbols-outlined input-icon">mail</span>
                <input
                  v-model="form.username"
                  id="username"
                  type="email"
                  placeholder="nombre@cablecauca.com"
                  autocomplete="username"
                />
              </div>
            </div>

            <div class="field">
              <label for="password">Contrase&ntilde;a</label>
              <div class="input-wrap">
                <span class="material-symbols-outlined input-icon">lock</span>
                <input
                  v-model="form.password"
                  id="password"
                  :type="showPassword ? 'text' : 'password'"
                  placeholder="********"
                  autocomplete="current-password"
                />
                <button type="button" class="toggle-pass" @click="showPassword = !showPassword">
                  <span class="material-symbols-outlined">{{ showPassword ? 'visibility_off' : 'visibility' }}</span>
                </button>
              </div>
            </div>

            <div class="row-actions">
              <label class="checkbox-label">
                <input type="checkbox" v-model="rememberMe" />
                <span>Recordarme</span>
              </label>
              <a href="#" class="link-forgot">¿Olvidaste tu contrase&ntilde;a?</a>
            </div>

            <button type="submit" class="btn-submit" :disabled="loading">
              {{ loading ? 'Entrando...' : 'Iniciar Sesi&oacute;n' }}
            </button>
          </form>

          <div class="form-footer">
            <p>¿Necesitas acceso? <a href="#">Contactar a TI</a></p>
          </div>
        </div>
      </div>
    </div>

    <footer class="login-footer">
      <div class="footer-inner">
        <div class="footer-brand">
          <span class="footer-brand-name">TeleCable</span>
          <p>&copy; 2026 TeleCable &mdash; Directorio Interno Corporativo</p>
        </div>
        <div class="footer-links">
          <a href="#">T&eacute;rminos y Condiciones</a>
          <a href="#">Pol&iacute;tica de Privacidad</a>
          <a href="#">Soporte T&eacute;cnico</a>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import { useRouter } from "vue-router";

const validCredentials = [
  { email: "soporte.riocauca@cablecauca.com", password: "admin123" },
  { email: "sistemas1@cablecauca.com", password: "sistemas1*" },
  { email: "sistemas2@cablecauca.com", password: "sistemas2*" },
  { email: "sistemas5@cablecauca.com", password: "sistemas5*" },
  { email: "sistemas.general@cablecauca.com", password: "sistemasgeneral123" },
  { email: "asistente.subgerencia@cablecauca.com", password: "subgerencia123" },
  { email: "sistemas@cablecauca.com", password: "sistemas123*" },
  { email: "retencioncomuneros@cablecauca.com", password: "retencioncomuneros123*" },
  { email: "subgerencia@cablecauca.com", password: "gerenciageneral123*" },
  { email: "recursoshumanos@cablecauca.com", password: "recursoshumanos123*" },
  { email: "contabilidad@netteconecta.com", password: "contabilidad123*" },
  { email: "proyectos.infraestructura@telecable.com.co", password: "proyectos123*"},
  { email: "instalaciones.cali@cablecauca.com", password: "instalaciones123*"}
];

const form = reactive({
  username: "",
  password: "",
});

const errorMessage = ref("");
const loading = ref(false);
const rememberMe = ref(false);
const showPassword = ref(false);
const router = useRouter();

function isValidEmail(value) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
}

async function handleLogin() {
  errorMessage.value = "";

  if (!form.username || !form.password) {
    errorMessage.value = "Por favor completa todos los campos.";
    return;
  }

  if (!isValidEmail(form.username)) {
    errorMessage.value = "Ingresa un email v&aacute;lido.";
    return;
  }

  loading.value = true;
  try {
    const match = validCredentials.some(
      (c) => c.email === form.username && c.password === form.password
    );

    if (match) {
      localStorage.setItem("usuarioAutenticado", "true");
      router.push("/home");
    } else {
      errorMessage.value = "Credenciales incorrectas. Intenta de nuevo.";
    }
  } catch (err) {
    console.error(err);
    errorMessage.value = "Ocurri&oacute; un error. Intenta m&aacute;s tarde.";
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.login-body {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px 20px 60px;
  background: #f1f3ff;
  position: relative;
  overflow: hidden;
  font-family: 'Hanken Grotesk', sans-serif;
}

.bg-decor {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
}

.bg-decor-1 {
  width: 400px;
  height: 400px;
  top: -120px;
  left: -120px;
  background: rgba(0, 61, 155, 0.08);
}

.bg-decor-2 {
  width: 400px;
  height: 400px;
  bottom: -120px;
  right: -120px;
  background: rgba(226, 37, 37, 0.04);
}

.login-card {
  display: flex;
  width: 100%;
  max-width: 900px;
  min-height: 500px;
  background: #ffffff;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0, 61, 155, 0.08);
  z-index: 1;
  animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* ===== LEFT SIDE ===== */
.login-side {
  width: 40%;
  background: linear-gradient(135deg, #003d9b 0%, #0052cc 100%);
  padding: 36px 32px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  color: #fffbff;
  position: relative;
  overflow: hidden;
}

.side-grid {
  position: absolute;
  inset: 0;
  background-image: radial-gradient(rgba(255, 255, 255, 0.1) 1px, transparent 1px);
  background-size: 24px 24px;
  opacity: 0.4;
}

.side-content {
  position: relative;
  z-index: 1;
}

.side-title {
  font-family: 'Hanken Grotesk', sans-serif;
  font-size: 34px;
  font-weight: 700;
  line-height: 1.1;
  letter-spacing: -0.02em;
  color: #c4d2ff;
  margin-bottom: 18px;
}

.side-desc {
  font-size: 16px;
  line-height: 1.5;
  color: rgba(196, 210, 255, 0.9);
  margin-bottom: 28px;
  max-width: 320px;
}

.side-features {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.side-feature {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.01em;
  opacity: 0.9;
  transition: all 0.25s ease;
  cursor: default;
}

.side-feature:hover {
  transform: translateX(4px);
  opacity: 1;
}

.feat-icon {
  width: 34px;
  height: 34px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  flex-shrink: 0;
}

.feat-icon .material-symbols-outlined {
  font-size: 18px;
  color: #c4d2ff;
}

/* ===== RIGHT SIDE ===== */
.login-form {
  width: 60%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 36px 32px;
}

.form-inner {
  width: 100%;
  max-width: 360px;
}

.form-header {
  text-align: center;
  margin-bottom: 24px;
}

.logo-wrap {
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
}

.logo-wrap img {
  width: 220px;
  height: auto;
  object-fit: contain;
}

.form-title {
  font-family: 'Hanken Grotesk', sans-serif;
  font-size: 22px;
  font-weight: 600;
  color: #041b3c;
  margin-bottom: 4px;
}

.form-subtitle {
  font-size: 15px;
  color: #434654;
}

/* ===== FORM ===== */
.error-message {
  display: flex;
  align-items: center;
  gap: 8px;
  background: #ffdad6;
  color: #93000a;
  padding: 12px 16px;
  border-radius: 8px;
  margin-bottom: 20px;
  font-size: 14px;
  font-weight: 500;
  animation: shake 0.4s;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  20%, 60% { transform: translateX(-3px); }
  40%, 80% { transform: translateX(3px); }
}

.error-message .material-symbols-outlined {
  font-size: 20px;
}

.field {
  margin-bottom: 16px;
}

.field label {
  display: block;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.01em;
  color: #434654;
  margin-bottom: 5px;
}

.input-wrap {
  position: relative;
  display: flex;
  align-items: center;
}

.input-wrap .input-icon {
  position: absolute;
  left: 14px;
  font-size: 20px;
  color: #737685;
  pointer-events: none;
}

.input-wrap input {
  width: 100%;
  padding: 10px 14px 10px 44px;
  font-size: 15px;
  font-family: 'Hanken Grotesk', sans-serif;
  border: 1px solid #c3c6d6;
  border-radius: 8px;
  background: #f1f3ff;
  color: #041b3c;
  outline: none;
  transition: all 0.2s ease;
}

.input-wrap input:focus {
  border-color: #003d9b;
  background: #ffffff;
  box-shadow: 0 0 0 3px rgba(0, 61, 155, 0.12);
}

.input-wrap input::placeholder {
  color: #737685;
}

.input-wrap:focus-within {
  transform: scale(1.01);
  transition: transform 0.2s ease;
}

.toggle-pass {
  position: absolute;
  right: 10px;
  background: none;
  border: none;
  cursor: pointer;
  color: #737685;
  display: flex;
  align-items: center;
  padding: 4px;
  transition: color 0.2s;
}

.toggle-pass:hover {
  color: #003d9b;
}

.toggle-pass .material-symbols-outlined {
  font-size: 20px;
}

.row-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 4px 0 20px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  color: #434654;
  transition: color 0.2s;
}

.checkbox-label:hover {
  color: #041b3c;
}

.checkbox-label input[type="checkbox"] {
  width: 16px;
  height: 16px;
  accent-color: #003d9b;
  border-radius: 3px;
}

.link-forgot {
  font-size: 12px;
  font-weight: 500;
  color: #003d9b;
  text-decoration: none;
  transition: all 0.2s;
}

.link-forgot:hover {
  text-decoration: underline;
}

.btn-submit {
  width: 100%;
  padding: 14px;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.01em;
  font-family: 'Hanken Grotesk', sans-serif;
  background: #003d9b;
  color: #ffffff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 61, 155, 0.2);
  transition: all 0.25s ease;
}

.btn-submit:hover:not(:disabled) {
  background: #0052cc;
  transform: scale(0.98);
  box-shadow: 0 6px 20px rgba(0, 61, 155, 0.3);
}

.btn-submit:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn-submit:active:not(:disabled) {
  transform: scale(0.97);
}

.form-footer {
  margin-top: 24px;
  padding-top: 16px;
  border-top: 1px solid #c3c6d6;
  text-align: center;
}

.form-footer p {
  font-size: 16px;
  color: #434654;
}

.form-footer a {
  color: #003d9b;
  font-weight: 700;
  text-decoration: none;
  transition: all 0.2s;
}

.form-footer a:hover {
  text-decoration: underline;
}

/* ===== FOOTER ===== */
.login-footer {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #f1f3ff;
  border-top: 1px solid #c3c6d6;
  padding: 12px 20px;
  z-index: 1;
}

.footer-inner {
  max-width: 900px;
  margin: 0 auto;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
}

.footer-brand {
  display: flex;
  align-items: center;
  gap: 12px;
}

.footer-brand-name {
  font-size: 14px;
  font-weight: 700;
  color: #003d9b;
}

.footer-brand p {
  font-size: 12px;
  font-weight: 500;
  color: #737685;
}

.footer-links {
  display: flex;
  gap: 20px;
}

.footer-links a {
  font-size: 12px;
  font-weight: 500;
  color: #737685;
  text-decoration: none;
  transition: color 0.2s;
}

.footer-links a:hover {
  color: #003d9b;
}

@media (max-width: 640px) {
  .footer-inner {
    flex-direction: column;
    text-align: center;
  }

  .footer-brand {
    flex-direction: column;
    gap: 4px;
  }

  .footer-links {
    flex-wrap: wrap;
    justify-content: center;
  }
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .login-card {
    flex-direction: column;
    max-width: 480px;
    min-height: auto;
  }

  .login-side {
    width: 100%;
    padding: 28px 24px;
  }

  .side-title {
    font-size: 28px;
  }

  .side-desc {
    font-size: 15px;
    margin-bottom: 24px;
  }

  .login-form {
    width: 100%;
    padding: 28px 20px;
  }

  .logo-wrap img {
    width: 180px;
  }
}

@media (max-width: 480px) {
  .login-wrapper { padding: 12px; }
  .login-card { border-radius: 12px; }
  .login-side { padding: 24px 20px; }
  .side-title { font-size: 24px; }
  .side-desc { font-size: 14px; margin-bottom: 20px; }
  .login-form { padding: 24px 16px; }
  .logo-wrap img { width: 150px; }
  .input-group input { font-size: 15px; padding: 12px 14px; }
  .login-btn { font-size: 15px; padding: 12px; }
  .footer-inner { flex-direction: column; text-align: center; gap: 8px; }
  .footer-brand { flex-direction: column; gap: 4px; }
  .footer-links { flex-wrap: wrap; justify-content: center; }
}
</style>
