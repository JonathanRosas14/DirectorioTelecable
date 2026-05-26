<template>
  <div class="oficina-detalle">
    <div class="detalle-head">
      <button @click="volver" class="btn-back">
        <i class="ph ph-arrow-left"></i>
        <span>Volver a Oficinas</span>
      </button>
      <h1 class="detalle-titulo">{{ oficina.nombre }}</h1>
    </div>

    <div class="detalle-grid">
      <div class="card">
        <div class="card-head">
          <i class="ph ph-info"></i>
          <h2>Información General</h2>
        </div>
        <div class="card-body">
          <div class="info-grid">
            <div class="info-item">
              <span class="info-label">Administrador</span>
              <span class="info-val">{{ oficina.administrador }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">PBX</span>
              <span class="info-val badge">{{ oficina.pbx }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Teléfono</span>
              <span class="info-val">{{ oficina.telefono }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Dirección</span>
              <span class="info-val">{{ oficina.direccion }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Ciudad</span>
              <span class="info-val">{{ oficina.ciudad }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Departamento</span>
              <span class="info-val">{{ oficina.departamento }}</span>
            </div>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-head">
          <i class="ph ph-map-pin"></i>
          <h2>Barrios Cubiertos</h2>
          <span class="card-badge">{{ oficina.barrios?.length || 0 }}</span>
        </div>
        <div class="card-body">
          <div class="tags">
            <span v-for="(barrio, i) in oficina.barrios" :key="i" class="tag">{{ barrio }}</span>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-head">
          <i class="ph ph-currency-circle-dollar"></i>
          <h2>Planes Disponibles</h2>
          <span class="card-badge">{{ oficina.planes?.length || 0 }}</span>
        </div>
        <div class="card-body">
          <div class="planes-grid">
            <div v-for="(plan, i) in oficina.planes" :key="i" class="plan-card">
              <div class="plan-head">
                <i class="ph ph-wifi-high"></i>
                <h3>{{ plan.nombre }}</h3>
              </div>
              <div class="plan-price">{{ plan.precio }}</div>
            </div>
          </div>
        </div>
      </div>

      <div class="card" v-if="oficina.puntosRecaudo && oficina.puntosRecaudo.length > 0">
        <div class="card-head">
          <i class="ph ph-storefront"></i>
          <h2>Puntos de Recaudo</h2>
          <span class="card-badge">{{ oficina.puntosRecaudo.length }}</span>
        </div>
        <div class="card-body">
          <div class="puntos-grid">
            <div v-for="(punto, i) in oficina.puntosRecaudo" :key="i" class="punto-item">
              <i class="ph ph-hand-coins"></i>
              <div>
                <span class="punto-name">{{ punto.nombre }}</span>
                <span v-if="punto.direccion" class="punto-dir">{{ punto.direccion }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="card" v-if="tienePersonalAdicional">
        <div class="card-head">
          <i class="ph ph-users"></i>
          <h2>Personal Adicional</h2>
          <span class="card-badge">{{ oficina.personalAdicional.length }}</span>
        </div>
        <div class="card-body">
          <div class="personal-grid">
            <div v-for="(persona, i) in oficina.personalAdicional" :key="i" class="persona-card">
              <div class="persona-head">
                <i class="ph ph-user-circle"></i>
                <div>
                  <h3>{{ persona.nombre }}</h3>
                  <p>{{ persona.cargo }}</p>
                </div>
              </div>
              <div class="persona-contact" v-if="persona.pbx || persona.telefono">
                <span v-if="persona.pbx"><strong>PBX:</strong> {{ persona.pbx }}</span>
                <span v-if="persona.telefono"><strong>Tel:</strong> {{ persona.telefono }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="card contact-card">
        <div class="card-head">
          <i class="ph ph-phone"></i>
          <h2>Información de Contacto</h2>
        </div>
        <div class="card-body">
          <div class="contact-grid">
            <a :href="'https://web.whatsapp.com/send?phone=57' + oficina.telefono" target="_blank" class="contact-item">
              <i class="ph ph-whatsapp-logo"></i>
              <div>
                <span class="contact-label">Teléfono</span>
                <span class="contact-val">{{ oficina.telefono }}</span>
              </div>
            </a>
            <div class="contact-item">
              <i class="ph ph-phone"></i>
              <div>
                <span class="contact-label">PBX</span>
                <span class="contact-val">{{ oficina.pbx }}</span>
              </div>
            </div>
            <div class="contact-item">
              <i class="ph ph-map-pin"></i>
              <div>
                <span class="contact-label">Ubicación</span>
                <span class="contact-val">{{ oficina.ciudad }}, {{ oficina.departamento }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const oficina = ref({});

onMounted(() => {
  const oficinaData = localStorage.getItem("oficinaSeleccionada");
  if (oficinaData) {
    oficina.value = JSON.parse(oficinaData);
  } else {
    router.push("/home/oficinas");
  }
});

const tienePersonalAdicional = computed(() => {
  return (
    oficina.value.personalAdicional &&
    oficina.value.personalAdicional.length > 0 &&
    oficina.value.personalAdicional[0].nombre !== "NO"
  );
});

const volver = () => {
  router.push("/home/oficinas");
};
</script>

<style scoped>
.oficina-detalle {
  padding: 36px 40px;
  min-height: calc(100vh - 72px);
  animation: fadeIn 0.4s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}

.detalle-head {
  margin-bottom: 32px;
}

.btn-back {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: var(--surface);
  border: 1.5px solid var(--warm-border);
  border-radius: var(--radius-sm);
  color: var(--warm-gray);
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all var(--transition);
  margin-bottom: 16px;
  font-family: var(--font-body);
}

.btn-back:hover {
  border-color: var(--teal);
  color: var(--teal);
  transform: translateX(-3px);
}

.detalle-titulo {
  font-family: var(--font-heading);
  font-size: 34px;
  font-weight: 600;
  color: var(--warm-dark);
  margin: 0;
  line-height: 1.2;
}

.detalle-grid {
  display: grid;
  gap: 24px;
}

.card {
  background: var(--surface);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
  border: 1px solid var(--warm-border);
  overflow: hidden;
  transition: all var(--transition);
}

.card:hover {
  box-shadow: var(--shadow-md);
}

.card-head {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 18px 22px;
  background: var(--cream);
  border-bottom: 1px solid var(--warm-border);
}

.card-head i {
  font-size: 1.3rem;
  color: var(--teal);
}

.card-head h2 {
  font-family: var(--font-heading);
  font-size: 18px;
  font-weight: 600;
  color: var(--warm-dark);
  margin: 0;
  flex: 1;
}

.card-badge {
  padding: 3px 10px;
  background: var(--teal);
  color: white;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}

.card-body {
  padding: 22px;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 20px;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.info-label {
  font-size: 12px;
  font-weight: 600;
  color: var(--warm-gray);
  text-transform: uppercase;
  letter-spacing: 0.4px;
}

.info-val {
  font-size: 15px;
  font-weight: 500;
  color: var(--warm-dark);
}

.info-val.badge {
  display: inline-block;
  padding: 4px 10px;
  background: rgba(201, 120, 60, 0.1);
  color: var(--terracotta);
  border-radius: 6px;
  font-weight: 600;
  width: fit-content;
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.tag {
  padding: 6px 14px;
  background: rgba(34, 114, 255, 0.07);
  color: var(--teal);
  border-radius: 20px;
  font-size: 13px;
  font-weight: 500;
  border: 1px solid rgba(34, 114, 255, 0.12);
  transition: all var(--transition);
}

.tag:hover {
  background: rgba(34, 114, 255, 0.12);
  transform: translateY(-1px);
}

.planes-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
}

.plan-card {
  padding: 20px;
  background: var(--cream);
  border: 1px solid var(--warm-border);
  border-radius: var(--radius-sm);
  transition: all var(--transition);
  text-align: center;
}

.plan-card:hover {
  border-color: var(--teal);
  transform: translateY(-3px);
  box-shadow: var(--shadow-md);
}

.plan-head {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-bottom: 12px;
}

.plan-head i {
  font-size: 1.3rem;
  color: var(--teal);
}

.plan-head h3 {
  font-size: 16px;
  font-weight: 600;
  color: var(--warm-dark);
  margin: 0;
}

.plan-price {
  font-size: 28px;
  font-weight: 700;
  color: var(--terracotta);
  padding: 10px 0;
  background: var(--surface);
  border-radius: var(--radius-sm);
  border: 1px solid var(--warm-border);
}

.puntos-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 12px;
}

.punto-item {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 14px;
  background: var(--cream);
  border: 1px solid var(--warm-border);
  border-radius: var(--radius-sm);
  transition: all var(--transition);
}

.punto-item:hover {
  border-color: var(--teal);
  transform: translateY(-1px);
}

.punto-item i {
  font-size: 1.5rem;
  color: var(--teal);
  opacity: 0.7;
}

.punto-item div {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.punto-name {
  font-size: 14px;
  font-weight: 600;
  color: var(--warm-dark);
}

.punto-dir {
  font-size: 12px;
  color: var(--warm-gray);
}

.personal-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 14px;
}

.persona-card {
  padding: 16px;
  background: var(--cream);
  border: 1px solid var(--warm-border);
  border-radius: var(--radius-sm);
  transition: all var(--transition);
}

.persona-card:hover {
  border-color: var(--teal);
  transform: translateY(-1px);
}

.persona-head {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 10px;
}

.persona-head i {
  font-size: 2rem;
  color: var(--teal);
  opacity: 0.7;
}

.persona-head h3 {
  font-size: 15px;
  font-weight: 600;
  color: var(--warm-dark);
  margin: 0 0 2px;
}

.persona-head p {
  font-size: 13px;
  color: var(--warm-gray);
  margin: 0;
}

.persona-contact {
  display: flex;
  gap: 16px;
  font-size: 13px;
  color: var(--warm-gray);
  padding-top: 10px;
  border-top: 1px solid var(--warm-border);
}

strong {
  color: var(--warm-dark);
}

.contact-card {
  border: 1px solid rgba(34, 114, 255, 0.15);
}

.contact-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 14px;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 18px;
  background: var(--cream);
  border: 1px solid var(--warm-border);
  border-radius: var(--radius-sm);
  text-decoration: none;
  transition: all var(--transition);
  cursor: pointer;
}

.contact-item:hover {
  border-color: var(--teal);
  transform: translateY(-2px);
  box-shadow: var(--shadow-sm);
}

.contact-item i {
  font-size: 1.6rem;
  color: var(--teal);
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(34, 114, 255, 0.08);
  border-radius: var(--radius-sm);
}

.contact-label {
  font-size: 12px;
  font-weight: 600;
  color: var(--warm-gray);
  text-transform: uppercase;
  letter-spacing: 0.4px;
  display: block;
  margin-bottom: 2px;
}

.contact-val {
  font-size: 14px;
  font-weight: 600;
  color: var(--warm-dark);
}

@media (max-width: 1024px) {
  .oficina-detalle { padding: 32px 28px; }
  .detalle-titulo { font-size: 30px; }
}

@media (max-width: 768px) {
  .oficina-detalle { padding: 24px 16px; }
  .detalle-titulo { font-size: 26px; }
  .detalle-desc { font-size: 16px; }
  .info-grid { grid-template-columns: 1fr; }
  .planes-grid { grid-template-columns: 1fr; }
  .puntos-grid { grid-template-columns: 1fr; }
  .personal-grid { grid-template-columns: 1fr; }
  .contact-grid { grid-template-columns: 1fr; }
}

@media (max-width: 480px) {
  .oficina-detalle { padding: 20px 12px; }
  .detalle-titulo { font-size: 22px; }
  .detalle-desc { font-size: 15px; }
  .section-title h2 { font-size: 18px; }
  .planes-grid { gap: 12px; }
}
</style>
