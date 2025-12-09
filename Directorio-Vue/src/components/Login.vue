<template>
  <div class="login-body">
    <div class="login-container">
      <div class="login-side">
        <div class="side-content">
          <h2>Bienvenido al directorio interno de Telecable</h2>
          <p>
            Accede a toda la informacion y detalles que necesitas de la empresa.
          </p>
        </div>
      </div>

      <div class="login-form-section">
        <div class="logo-container">
          <img src="../assets/Logo.png" alt="Logo" />
        </div>

        <h1 class="form-title">Iniciar Sesión</h1>
        <p class="form-subtitle">Ingresa tus credenciales para continuar</p>

        <div v-if="errorMessage" class="error-message" role="alert">
          <span>⚠️</span>
          <span>{{ errorMessage }}</span>
        </div>

        <form @submit.prevent="handleLogin" novalidate>
          <div class="form-group">
            <label for="username">Correo Electrónico</label>
            <div class="input-wrapper">
              <span class="input-icon">📧</span>
              <input
                v-model="form.username"
                class="input-login"
                required
                name="username"
                id="username"
                type="email"
                placeholder="Ingresa tu correo"
                autocomplete="username"
              />
            </div>
          </div>

          <div class="form-group">
            <label for="password">Contraseña</label>
            <div class="input-wrapper">
              <span class="input-icon">🔐</span>
              <input
                v-model="form.password"
                class="input-login"
                required
                name="password"
                id="password"
                type="password"
                placeholder="Ingresa tu contraseña"
                autocomplete="current-password"
              />
            </div>
          </div>

          <div class="remember-forgot">
            <label class="remember-me">
              <input type="checkbox" v-model="rememberMe" />
              <span>Recordarme</span>
            </label>
            <a href="#" class="forgot-password">¿Olvidaste tu contraseña?</a>
          </div>

          <div class="form-group">
            <input
              class="input-login"
              :disabled="loading"
              type="submit"
              :value="loading ? 'Entrando...' : 'Iniciar Sesión'"
            />
          </div>
        </form>
      </div>
    </div>
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
  { email: "proyectos.infraestructura@telecable.com.co", password: "proyectos123*"}
];

const form = reactive({
  username: "",
  password: "",
});

const errorMessage = ref("");
const loading = ref(false);
const rememberMe = ref(false);
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
    errorMessage.value = "Ingresa un email válido.";
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
    errorMessage.value = "Ocurrió un error. Intenta más tarde.";
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.login-body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, sans-serif;
  background: linear-gradient(135deg, #1e3c72 0%, #2a5298 50%, #7e8ba3 100%);
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  position: relative;
  overflow: hidden;
}

.login-body::before {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(
      30deg,
      rgba(255, 255, 255, 0.03) 12%,
      transparent 12.5%,
      transparent 87%,
      rgba(255, 255, 255, 0.03) 87.5%,
      rgba(255, 255, 255, 0.03)
    ),
    linear-gradient(
      150deg,
      rgba(255, 255, 255, 0.03) 12%,
      transparent 12.5%,
      transparent 87%,
      rgba(255, 255, 255, 0.03) 87.5%,
      rgba(255, 255, 255, 0.03)
    ),
    linear-gradient(
      30deg,
      rgba(255, 255, 255, 0.03) 12%,
      transparent 12.5%,
      transparent 87%,
      rgba(255, 255, 255, 0.03) 87.5%,
      rgba(255, 255, 255, 0.03)
    ),
    linear-gradient(
      150deg,
      rgba(255, 255, 255, 0.03) 12%,
      transparent 12.5%,
      transparent 87%,
      rgba(255, 255, 255, 0.03) 87.5%,
      rgba(255, 255, 255, 0.03)
    );
  background-size: 80px 140px;
  background-position: 0 0, 0 0, 40px 70px, 40px 70px;
  z-index: 0;
}

.login-container {
  position: relative;
  z-index: 1;
  display: flex;
  width: 100%;
  max-width: 1000px;
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 20px 80px rgba(0, 0, 0, 0.3);
  animation: slideIn 0.8s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.login-side {
  flex: 1;
  background: linear-gradient(135deg, #2a5298 0%, #1e3c72 100%);
  padding: 60px 40px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: white;
  position: relative;
  overflow: hidden;
}

.login-side::before {
  content: "";
  position: absolute;
  width: 300px;
  height: 300px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  top: -100px;
  right: -100px;
}

.login-side::after {
  content: "";
  position: absolute;
  width: 200px;
  height: 200px;
  background: rgba(255, 255, 255, 0.08);
  border-radius: 50%;
  bottom: -50px;
  left: -50px;
}

.side-content {
  position: relative;
  z-index: 1;
  text-align: center;
}

.side-content h2 {
  font-size: 32px;
  font-weight: 700;
  margin-bottom: 20px;
  line-height: 1.2;
}

.side-content p {
  font-size: 16px;
  opacity: 0.9;
  line-height: 1.6;
}

.login-form-section {
  flex: 1;
  padding: 60px 50px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.logo-container {
  text-align: center;
  margin-bottom: 40px;
}

.logo-container img {
  width: 250px;
  height: 65px;
  object-fit: contain;
}

.form-title {
  font-size: 28px;
  font-weight: 700;
  color: #1e3c72;
  margin-bottom: 10px;
  text-align: center;
}

.form-subtitle {
  font-size: 14px;
  color: #64748b;
  margin-bottom: 35px;
  text-align: center;
}

.error-message {
  background: #fee;
  border-left: 4px solid #dc2626;
  color: #991b1b;
  border-radius: 8px;
  padding: 14px 18px;
  margin-bottom: 24px;
  font-size: 14px;
  font-weight: 500;
  display: flex;
  align-items: center;
  gap: 10px;
  animation: shake 0.5s;
}

@keyframes shake {
  0%,
  100% {
    transform: translateX(0);
  }
  10%,
  30%,
  50%,
  70%,
  90% {
    transform: translateX(-3px);
  }
  20%,
  40%,
  60%,
  80% {
    transform: translateX(3px);
  }
}

.form-group {
  margin-bottom: 24px;
}

label {
  display: block;
  font-size: 13px;
  font-weight: 600;
  color: #334155;
  margin-bottom: 8px;
  letter-spacing: 0.3px;
}

.input-wrapper {
  position: relative;
}

.input-icon {
  position: absolute;
  left: 16px;
  top: 50%;
  transform: translateY(-50%);
  color: #94a3b8;
  font-size: 18px;
}

.input-login[type="text"],
.input-login[type="email"],
.input-login[type="password"] {
  width: 100%;
  padding: 14px 16px 14px 46px;
  font-size: 15px;
  border: 1.5px solid #e2e8f0;
  border-radius: 10px;
  background: #f8fafc;
  transition: all 0.3s ease;
  outline: none;
  color: #1e293b;
  font-weight: 400;
}

.input-login[type="text"]:focus,
.input-login[type="email"]:focus,
.input-login[type="password"]:focus {
  border-color: #2a5298;
  background: white;
  box-shadow: 0 0 0 4px rgba(42, 82, 152, 0.08);
}

.input-login[type="text"]::placeholder,
.input-login[type="email"]::placeholder,
.input-login[type="password"]::placeholder {
  color: #94a3b8;
}

.remember-forgot {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 28px;
  font-size: 14px;
}

.remember-me {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #64748b;
  cursor: pointer;
}

.remember-me input[type="checkbox"] {
  width: 16px;
  height: 16px;
  cursor: pointer;
}

.forgot-password {
  color: #2a5298;
  text-decoration: none;
  font-weight: 600;
  transition: color 0.3s ease;
}

.forgot-password:hover {
  color: #1e3c72;
}

.input-login[type="submit"] {
  width: 100%;
  background: linear-gradient(135deg, #2a5298 0%, #1e3c72 100%);
  color: white;
  padding: 16px 20px;
  font-size: 16px;
  font-weight: 600;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  box-shadow: 0 4px 16px rgba(42, 82, 152, 0.3);
  transition: all 0.3s ease;
  letter-spacing: 0.5px;
}

.input-login[type="submit"]:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 6px 24px rgba(42, 82, 152, 0.4);
}

.input-login[type="submit"]:active:not(:disabled) {
  transform: translateY(0);
}

.input-login[type="submit"]:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}


@media (max-width: 768px) {
  .login-container {
    flex-direction: column;
    max-width: 450px;
  }

  .login-side {
    padding: 40px 30px;
  }

  .side-content h2 {
    font-size: 24px;
  }

  .login-form-section {
    padding: 40px 30px;
  }
}
</style>
