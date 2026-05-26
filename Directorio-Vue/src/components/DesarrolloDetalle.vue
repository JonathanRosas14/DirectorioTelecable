<template>
  <div class="desarrollo-detalle">
    <div class="detalle-head">
      <button @click="volver" class="btn-back">
        <i class="ph ph-arrow-left"></i>
        <span>Volver a Desarrollos</span>
      </button>
      <h1 class="detalle-titulo">{{ desarrollo.nombre_desarrollo }}</h1>
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
              <span class="info-label">Encargado</span>
              <span class="info-val">{{ desarrollo.Encargado_desarrollo }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Teléfono</span>
              <span class="info-val badge">{{ desarrollo.telefono_desarrollo }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Correo Electrónico</span>
              <span class="info-val email">{{ desarrollo.correo_desarrollo }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Dirección</span>
              <span class="info-val">{{ desarrollo.direccion_desarrollo }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Ciudad</span>
              <span class="info-val">{{ desarrollo.ciudad_desarrollo }}</span>
            </div>
            <div class="info-item">
              <span class="info-label">Departamento</span>
              <span class="info-val">{{ desarrollo.departamento_desarrollo }}</span>
            </div>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-head">
          <i class="ph ph-map-pin"></i>
          <h2>Barrios Cubiertos</h2>
          <span class="card-badge">{{ desarrollo.barrios_desarrollo.length }}</span>
        </div>
        <div class="card-body">
          <div class="tags">
            <span v-for="(barrio, i) in desarrollo.barrios_desarrollo" :key="i" class="tag">{{ barrio }}</span>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-head">
          <i class="ph ph-currency-circle-dollar"></i>
          <h2>Planes Disponibles</h2>
          <span class="card-badge">{{ desarrollo.planes_desarrollo.length }}</span>
        </div>
        <div class="card-body">
          <div class="planes-grid">
            <div v-for="(plan, i) in desarrollo.planes_desarrollo" :key="i" class="plan-card">
              <div class="plan-head">
                <i class="ph ph-wifi-high"></i>
                <h3>{{ plan.nombre_plan }}</h3>
              </div>
              <div class="plan-price">{{ plan.precio_plan }}</div>
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
            <a :href="'tel:' + desarrollo.telefono_desarrollo" class="contact-item">
              <i class="ph ph-phone-call"></i>
              <div>
                <span class="contact-label">Teléfono</span>
                <span class="contact-val">{{ desarrollo.telefono_desarrollo }}</span>
              </div>
            </a>
            <a :href="'mailto:' + desarrollo.correo_desarrollo" class="contact-item">
              <i class="ph ph-envelope"></i>
              <div>
                <span class="contact-label">Email</span>
                <span class="contact-val">{{ desarrollo.correo_desarrollo }}</span>
              </div>
            </a>
            <div class="contact-item">
              <i class="ph ph-map-pin"></i>
              <div>
                <span class="contact-label">Ubicación</span>
                <span class="contact-val">{{ desarrollo.ciudad_desarrollo }}, {{ desarrollo.departamento_desarrollo }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const desarrollo = ref({
  nombre_desarrollo: "",
  Encargado_desarrollo: "",
  telefono_desarrollo: "",
  correo_desarrollo: "",
  direccion_desarrollo: "",
  ciudad_desarrollo: "",
  departamento_desarrollo: "",
  barrios_desarrollo: [],
  planes_desarrollo: [],
});

onMounted(() => {
  const desarrolloData = localStorage.getItem("desarrolloSeleccionado");
  if (desarrolloData) {
    desarrollo.value = JSON.parse(desarrolloData);
  } else {
    router.push("/home/desarrollos");
  }
});

const volver = () => {
  router.push("/home/desarrollos");
};
</script>

<style scoped>
.desarrollo-detalle {
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

.info-val.email {
  color: var(--warm-dark);
  text-decoration: underline;
  cursor: pointer;
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
  .desarrollo-detalle { padding: 32px 28px; }
  .detalle-titulo { font-size: 30px; }
}

@media (max-width: 768px) {
  .desarrollo-detalle { padding: 24px 16px; }
  .detalle-titulo { font-size: 26px; }
  .detalle-desc { font-size: 16px; }
  .info-grid { grid-template-columns: 1fr; }
  .planes-grid { grid-template-columns: 1fr; }
  .contact-grid { grid-template-columns: 1fr; }
}

@media (max-width: 480px) {
  .desarrollo-detalle { padding: 20px 12px; }
  .detalle-titulo { font-size: 22px; }
  .detalle-desc { font-size: 15px; }
  .section-title h2 { font-size: 18px; }
  .planes-grid { gap: 12px; }
}
</style>
