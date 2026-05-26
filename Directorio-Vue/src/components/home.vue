<template>
  <div class="layout">
    <header class="layout-header">
      <div class="header-inner">
        <div class="header-left">
          <button class="hamburger" @click="toggleMobileNav" aria-label="Men&uacute;">
            <span class="material-symbols-outlined">menu</span>
          </button>
          <div class="header-logo">
            <img src="../assets/Logo.png" alt="Telecable" class="logo" />
          </div>
        </div>

        <nav class="header-nav">
          <router-link to="/home/oficinas" class="nav-link" :class="{ active: $route.path === '/home/oficinas' }">
            <span class="material-symbols-outlined">corporate_fare</span>
            <span>Oficinas</span>
          </router-link>
          <router-link to="/home/desarrollos" class="nav-link" :class="{ active: $route.path === '/home/desarrollos' }">
            <span class="material-symbols-outlined">language</span>
            <span>Desarrollos</span>
          </router-link>
          <router-link to="/home/extensiones" class="nav-link" :class="{ active: $route.path === '/home/extensiones' }">
            <span class="material-symbols-outlined">phone</span>
            <span>Extensiones</span>
          </router-link>
          <router-link to="/home/formularios" class="nav-link" :class="{ active: $route.path === '/home/formularios' }">
            <span class="material-symbols-outlined">description</span>
            <span>Formularios</span>
          </router-link>
          <router-link to="/home/infogeneral" class="nav-link" :class="{ active: $route.path === '/home/infogeneral' }">
            <span class="material-symbols-outlined">info</span>
            <span>Informaci&oacute;n General</span>
          </router-link>
        </nav>

        <!-- Mobile overlay -->
        <div v-if="showMobileNav" class="mobile-overlay" @click="showMobileNav = false"></div>

        <!-- Mobile drawer -->
        <aside class="mobile-drawer" :class="{ open: showMobileNav }">
          <div class="drawer-head">
            <img src="../assets/Logo.png" alt="Telecable" class="drawer-logo" />
            <button class="drawer-close" @click="showMobileNav = false">
              <span class="material-symbols-outlined">close</span>
            </button>
          </div>
          <nav class="drawer-nav">
            <router-link to="/home/oficinas" class="drawer-link" :class="{ active: $route.path === '/home/oficinas' }" @click="showMobileNav = false">
              <span class="material-symbols-outlined">corporate_fare</span>
              <span>Oficinas</span>
            </router-link>
            <router-link to="/home/desarrollos" class="drawer-link" :class="{ active: $route.path === '/home/desarrollos' }" @click="showMobileNav = false">
              <span class="material-symbols-outlined">language</span>
              <span>Desarrollos</span>
            </router-link>
            <router-link to="/home/extensiones" class="drawer-link" :class="{ active: $route.path === '/home/extensiones' }" @click="showMobileNav = false">
              <span class="material-symbols-outlined">phone</span>
              <span>Extensiones</span>
            </router-link>
            <router-link to="/home/formularios" class="drawer-link" :class="{ active: $route.path === '/home/formularios' }" @click="showMobileNav = false">
              <span class="material-symbols-outlined">description</span>
              <span>Formularios</span>
            </router-link>
            <router-link to="/home/infogeneral" class="drawer-link" :class="{ active: $route.path === '/home/infogeneral' }" @click="showMobileNav = false">
              <span class="material-symbols-outlined">info</span>
              <span>Informaci&oacute;n General</span>
            </router-link>
          </nav>
          <div class="drawer-footer">
            <button @click="handleLogout" class="drawer-logout">
              <span class="material-symbols-outlined">logout</span>
              <span>Cerrar Sesi&oacute;n</span>
            </button>
          </div>
        </aside>

        <div class="header-actions">
          <button class="icon-btn">
            <span class="material-symbols-outlined">notifications</span>
          </button>
          <button class="icon-btn">
            <span class="material-symbols-outlined">settings</span>
          </button>
          <div class="user-area">
            <button class="user-btn" @click="toggleUserMenu">
              <div class="user-avatar">
                <span class="material-symbols-outlined">person</span>
              </div>
              <span class="user-name">Admin Usuario</span>
              <span class="material-symbols-outlined arrow">expand_more</span>
            </button>

            <div v-if="showUserMenu" class="user-dropdown">
              <div class="dropdown-header">
                <div class="dropdown-avatar">
                  <span class="material-symbols-outlined">person</span>
                </div>
                <div>
                  <p class="dropdown-name">Admin Usuario</p>
                </div>
              </div>
              <div class="dropdown-divider"></div>
              <button @click="handleLogout" class="dropdown-item">
                <span class="material-symbols-outlined">logout</span>
                <span>Cerrar Sesi&oacute;n</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </header>

    <section class="content">
      <router-view></router-view>
    </section>

    <footer class="layout-footer">
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
import { ref, onMounted, onUnmounted } from 'vue'

const showUserMenu = ref(false)
const showMobileNav = ref(false)

const toggleUserMenu = () => {
  showUserMenu.value = !showUserMenu.value
}

const toggleMobileNav = () => {
  showMobileNav.value = !showMobileNav.value
}

const handleLogout = () => {
  showUserMenu.value = false
  localStorage.removeItem('usuarioAutenticado')
  window.location.href = '/'
}

const handleClickOutside = (event) => {
  const userBtn = document.querySelector('.user-btn')
  const dropdown = document.querySelector('.user-dropdown')

  if (userBtn && dropdown &&
      !userBtn.contains(event.target) &&
      !dropdown.contains(event.target)) {
    showUserMenu.value = false
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<style scoped>
.layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.layout-header {
  position: sticky;
  top: 0;
  z-index: 100;
  background: var(--surface);
  border-bottom: 1px solid var(--warm-border);
}

.header-inner {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 40px;
  height: 80px;
  gap: 24px;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-shrink: 0;
}

.hamburger {
  display: none;
  width: 40px;
  height: 40px;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
  border-radius: 8px;
  color: var(--warm-gray);
  cursor: pointer;
  transition: all 0.2s;
}

.hamburger:hover {
  background: var(--cream);
  color: var(--teal);
}

.hamburger .material-symbols-outlined {
  font-size: 24px;
}

.header-logo {
  flex-shrink: 0;
}

.logo {
  height: 44px;
  width: auto;
  object-fit: contain;
}

.header-nav {
  display: flex;
  align-items: center;
  gap: 6px;
  flex: 1;
  justify-content: center;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 6px;
  text-decoration: none;
  color: var(--warm-gray);
  padding: 8px 14px;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.01em;
  transition: color 0.2s;
  border-bottom: 2px solid transparent;
  white-space: nowrap;
}

.nav-link .material-symbols-outlined {
  font-size: 20px;
}

.nav-link:hover {
  color: var(--teal);
}

.nav-link.active {
  color: var(--teal);
  border-bottom-color: var(--teal);
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0;
}

.icon-btn {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
  border-radius: 8px;
  color: var(--warm-gray);
  cursor: pointer;
  transition: all 0.2s;
}

.icon-btn:hover {
  background: var(--cream);
  color: var(--teal);
}

.icon-btn .material-symbols-outlined {
  font-size: 22px;
}

.user-area {
  position: relative;
  padding-left: 12px;
  margin-left: 4px;
  border-left: 1px solid var(--warm-border);
}

.user-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 4px 8px 4px 4px;
  background: transparent;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s;
  font-family: var(--font-body);
}

.user-btn:hover {
  background: var(--cream);
}

.user-avatar {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--cream);
  border-radius: 50%;
  border: 1px solid var(--warm-border);
  color: var(--warm-gray);
}

.user-avatar .material-symbols-outlined {
  font-size: 20px;
}

.user-name {
  font-size: 14px;
  font-weight: 600;
  color: var(--warm-dark);
}

.arrow {
  font-size: 18px;
  color: var(--warm-gray-light);
  transition: transform 0.2s;
}

.user-btn:hover .arrow {
  transform: translateY(2px);
}

.user-dropdown {
  position: absolute;
  top: calc(100% + 8px);
  right: 0;
  width: 240px;
  background: var(--surface);
  border: 1px solid var(--warm-border);
  border-radius: 12px;
  box-shadow: var(--shadow-lg);
  z-index: 1000;
  overflow: hidden;
  animation: dropdownSlide 0.15s ease;
}

@keyframes dropdownSlide {
  from {
    opacity: 0;
    transform: translateY(-4px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.dropdown-header {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px;
}

.dropdown-avatar {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--cream);
  border-radius: 50%;
  color: var(--warm-gray);
}

.dropdown-avatar .material-symbols-outlined {
  font-size: 22px;
}

.dropdown-name {
  font-size: 15px;
  font-weight: 600;
  color: var(--warm-dark);
}

.dropdown-divider {
  height: 1px;
  background: var(--warm-border);
}

.dropdown-item {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  background: transparent;
  border: none;
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
  color: var(--warm-dark);
  text-align: left;
  font-family: var(--font-body);
  transition: background 0.2s;
}

.dropdown-item:hover {
  background: #ffdad6;
  color: #93000a;
}

.dropdown-item .material-symbols-outlined {
  font-size: 20px;
}

.content {
  flex: 1;
}

.layout-footer {
  background: var(--cream);
  border-top: 1px solid var(--warm-border);
  padding: 14px 20px;
}

.footer-inner {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
}

.footer-brand {
  display: flex;
  align-items: center;
  gap: 12px;
}

.footer-brand-name {
  font-size: 14px;
  font-weight: 700;
  color: var(--teal);
}

.footer-brand p {
  font-size: 12px;
  font-weight: 500;
  color: var(--warm-gray-light);
}

.footer-links {
  display: flex;
  gap: 20px;
}

.footer-links a {
  font-size: 12px;
  font-weight: 500;
  color: var(--warm-gray-light);
  text-decoration: none;
  transition: color 0.2s;
}

.footer-links a:hover {
  color: var(--teal);
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

/* ===== MOBILE DRAWER ===== */
.mobile-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.3);
  z-index: 200;
}

.mobile-drawer {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  width: 280px;
  background: var(--surface);
  z-index: 300;
  flex-direction: column;
  box-shadow: 2px 0 20px rgba(0, 0, 0, 0.1);
  transform: translateX(-100%);
  transition: transform 0.25s ease;
}

.mobile-drawer.open {
  transform: translateX(0);
}

.drawer-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 16px;
  border-bottom: 1px solid var(--warm-border);
}

.drawer-logo {
  height: 32px;
  width: auto;
  object-fit: contain;
}

.drawer-close {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
  border-radius: 8px;
  color: var(--warm-gray);
  cursor: pointer;
  transition: all 0.2s;
}

.drawer-close:hover {
  background: var(--cream);
  color: var(--warm-dark);
}

.drawer-close .material-symbols-outlined {
  font-size: 22px;
}

.drawer-nav {
  flex: 1;
  padding: 12px 8px;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.drawer-link {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  text-decoration: none;
  color: var(--warm-gray);
  border-radius: 8px;
  font-size: 15px;
  font-weight: 500;
  transition: all 0.2s;
}

.drawer-link:hover {
  background: var(--cream);
  color: var(--warm-dark);
}

.drawer-link.active {
  background: var(--cream);
  color: var(--teal);
  font-weight: 600;
}

.drawer-link .material-symbols-outlined {
  font-size: 22px;
}

.drawer-footer {
  padding: 12px 8px;
  border-top: 1px solid var(--warm-border);
}

.drawer-logout {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  background: transparent;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 15px;
  font-weight: 500;
  color: var(--warm-gray);
  font-family: var(--font-body);
  transition: all 0.2s;
}

.drawer-logout:hover {
  background: #ffdad6;
  color: #93000a;
}

.drawer-logout .material-symbols-outlined {
  font-size: 22px;
}

@media (max-width: 1024px) {
  .hamburger {
    display: flex;
  }

  .header-nav {
    display: none;
  }

  .mobile-overlay,
  .mobile-drawer {
    display: flex;
  }

  .header-inner {
    padding: 0 20px;
    height: 64px;
  }
}

@media (max-width: 768px) {
  .logo {
    height: 32px;
  }

  .user-name {
    display: none;
  }
}

@media (max-width: 480px) {
  .header-inner { padding: 0 12px; height: 56px; }
  .logo { height: 26px; }
  .drawer-inner { padding: 20px 16px; }
  .footer-inner { flex-direction: column; text-align: center; gap: 8px; padding: 16px 20px; }
  .footer-brand { flex-direction: column; gap: 4px; }
  .footer-links { flex-wrap: wrap; justify-content: center; }
}
</style>
