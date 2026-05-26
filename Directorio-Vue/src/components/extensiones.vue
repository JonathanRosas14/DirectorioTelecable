<template>
  <div class="layout-extensiones">
    <div class="page-head">
      <div>
        <h1 class="page-title">Directorio de Extensiones (PBX)</h1>
        <p class="page-desc">Consulta las extensiones telef&oacute;nicas del personal</p>
      </div>
    </div>

    <div class="filter-bar">
      <div class="search-wrap">
        <span class="material-symbols-outlined search-icon">search</span>
        <input v-model="filtroTexto" type="text" placeholder="Buscar por nombre, cargo o extensi&oacute;n..." />
      </div>
      <div class="filter-selects">
        <select v-model="filtroCategoria" class="select">
          <option value="Todos">Todas las categor&iacute;as</option>
          <option value="Administrador">Administradores</option>
          <option value="Retencion">Retenci&oacute;n</option>
          <option value="Caja">Caja</option>
          <option value="Cartera">Cartera</option>
          <option value="Contabilidad">Contabilidad</option>
          <option value="Sistemas">Sistemas</option>
          <option value="Gerencia">Gerencia</option>
        </select>
      </div>
    </div>

    <div class="stats">
      <div class="stat-card">
        <span class="material-symbols-outlined stat-icon">phone</span>
        <div>
          <span class="stat-label">Total Extensiones</span>
          <span class="stat-val">{{ extensionesFiltradas.length }}</span>
        </div>
      </div>
      <div class="stat-card">
        <span class="material-symbols-outlined stat-icon">business</span>
        <div>
          <span class="stat-label">Oficinas</span>
          <span class="stat-val">{{ totalOficinas }}</span>
        </div>
      </div>
      <div class="stat-card">
        <span class="material-symbols-outlined stat-icon">badge</span>
        <div>
          <span class="stat-label">Administrativos</span>
          <span class="stat-val">{{ totalAdministrativos }}</span>
        </div>
      </div>
    </div>

    <div class="table-container">
      <div class="table-head">
        <h2>Extensiones PBX</h2>
        <span class="count-badge">{{ extensionesFiltradas.length }} registros</span>
      </div>
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th>Nombre</th>
              <th>Cargo</th>
              <th>Extensi&oacute;n</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(ext, index) in extensionesPaginadas" :key="index">
              <td>
                <div class="cell-name">
                  <span class="material-symbols-outlined cell-icon">person</span>
                  <span>{{ ext.nombre_personal }}</span>
                </div>
              </td>
              <td>
                <span class="role-badge" :class="getCategoriaClass(ext.cargo)">{{ ext.cargo }}</span>
              </td>
              <td><span class="ext-badge">{{ ext.extension }}</span></td>
            </tr>
          </tbody>
        </table>
        <div v-if="extensionesFiltradas.length === 0" class="empty">
          <span class="material-symbols-outlined">search_off</span>
          <h3>No se encontraron extensiones</h3>
          <p>Intenta ajustar los filtros de b&uacute;squeda</p>
        </div>
      </div>
      <div class="table-foot">
        <span class="foot-info" v-if="extensionesFiltradas.length > 0">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} a {{ Math.min(currentPage * itemsPerPage, extensionesFiltradas.length) }} de {{ extensionesFiltradas.length }} registros
        </span>
        <div class="pagination" v-if="totalPages > 1">
          <button class="page-btn" :disabled="currentPage === 1" @click="irPagina(currentPage - 1)">
            <span class="material-symbols-outlined">chevron_left</span>
          </button>
          <button
            v-for="pagina in totalPages"
            :key="pagina"
            class="page-btn"
            :class="{ active: pagina === currentPage }"
            @click="irPagina(pagina)"
          >{{ pagina }}</button>
          <button class="page-btn" :disabled="currentPage === totalPages" @click="irPagina(currentPage + 1)">
            <span class="material-symbols-outlined">chevron_right</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const filtroTexto = ref("");
const filtroCategoria = ref("Todos");
const currentPage = ref(1);
const itemsPerPage = 6;

const pbxData = [
  { nombre_personal: "Erika Rivas", cargo: "Administrador Oficina Ceibas", extension: "1125" },
  { nombre_personal: "Sandra Balanta", cargo: "Administrador Oficina Mariano Ramos", extension: "1221" },
  { nombre_personal: "Diana Carolina Dorado Guauña", cargo: "Administrador Oficina Villa Nueva", extension: "1117" },
  { nombre_personal: "Maria Eugenia Diaz", cargo: "Administrador Oficina Poblado", extension: "1135" },
  { nombre_personal: "Alejandra Giraldo", cargo: "Administrador Oficina Rio Cauca", extension: "1127" },
  { nombre_personal: "Vanessa Villegas G", cargo: "Administrador Oficina Cordoba Reservado", extension: "1130" },
  { nombre_personal: "Claudia Vargas", cargo: "Administrador Oficina Comuneros", extension: "1112" },
  { nombre_personal: "Brush Zapata", cargo: "Retencion Comuneros", extension: "1212" },
  { nombre_personal: "Ana Yibe Fontal", cargo: "Caja Comuneros", extension: "1116" },
  { nombre_personal: "Leidy Johana Ospina", cargo: "Administrador Oficina Marroquin", extension: "1136" },
  { nombre_personal: "Maira Hernandez", cargo: "Retencion y Caja Marroquin", extension: "1105" },
  { nombre_personal: "Sharon Fuentes", cargo: "Administrador Oficina Chorros", extension: "1110" },
  { nombre_personal: "Jhoiner Arturo Barbosa Otalvaro", cargo: "Administrador Oficina Montebello", extension: "1211" },
  { nombre_personal: "Mayely Mosquera", cargo: "Administrador Oficina Siloe", extension: "1111" },
  { nombre_personal: "Angelica Hurtado Silva", cargo: "Administrador Oficina Cerrito", extension: "1211" },
  { nombre_personal: "Maria José Arias Hernández Acevedo", cargo: "Administrador Oficina Andalucia", extension: "1215" },
  { nombre_personal: "Maria del Carmen Morales", cargo: "Administrador Oficina Tulua", extension: "1207" },
  { nombre_personal: "Claudia Patricia Jaramillo Montoya", cargo: "Administrador Oficina Tarqui", extension: "1104" },
  { nombre_personal: "Maylhen Melo", cargo: "Administrador Oficina Florida", extension: "1107" },
  { nombre_personal: "Lizeth Johanna", cargo: "Retencion Florida", extension: "1118" },
  { nombre_personal: "Sebastian Prada", cargo: "Cartera Florida y Oficinas fuera de cali", extension: "1114" },
  { nombre_personal: "Mayra Alejandra Rivera", cargo: "Administrador Oficina Pradera", extension: "1220" },
  { nombre_personal: "Jackeline Rondon", cargo: "Caja Pradera", extension: "1108" },
  { nombre_personal: "Nasly Johanna Hernández", cargo: "Subgerencia", extension: "1102" },
  { nombre_personal: "Valentina Tamayo Ortega", cargo: "Asistente de gerencia", extension: "1204" },
  { nombre_personal: "Katherine Muñoz", cargo: "Recursos Humanos", extension: "1217" },
  { nombre_personal: "Anyela Viera", cargo: "Almacén - Sistemas", extension: "1133" },
  { nombre_personal: "David Solano", cargo: "Proyectos", extension: "1126" },
  { nombre_personal: "Carlos Bejarano", cargo: "Portería", extension: "1120" },
  { nombre_personal: "Karina Mariño", cargo: "Jefe Cartera", extension: "1201" },
  { nombre_personal: "Valeria Varona", cargo: "Auxiliar Cartera", extension: "1101" },
  { nombre_personal: "Luna Mora", cargo: "Auxiliar Cartera", extension: "1203" },
  { nombre_personal: "Natalia Valencia", cargo: "Contabilidad Telecable", extension: "1103" },
  { nombre_personal: "Monica Marcela Albaran Castaño", cargo: "Contabilidad Home tv", extension: "1218" },
  { nombre_personal: "Eliana Erazo", cargo: "Contabilidad Telecable", extension: "1100" },
  { nombre_personal: "Lina Marcela", cargo: "Contabilidad Cable Cauca", extension: "1100" },
  { nombre_personal: "Pedro Felipe Lopez", cargo: "Sistemas - Administrativo", extension: "1124" },
  { nombre_personal: "Sergio Gomez", cargo: "Sistemas - Call Center", extension: "1119" },
  { nombre_personal: "Jerson Brand", cargo: "Sistemas - Call Center", extension: "1301" },
  { nombre_personal: "Sandra Patricia Escobar", cargo: "Sistemas - Call Center", extension: "1132" },
];

const extensionesFiltradas = computed(() => {
  return pbxData.filter((ext) => {
    const matchTexto =
      !filtroTexto.value ||
      ext.nombre_personal.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      ext.cargo.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      ext.extension.includes(filtroTexto.value);
    const matchCategoria =
      filtroCategoria.value === "Todos" ||
      ext.cargo.toLowerCase().includes(filtroCategoria.value.toLowerCase());
    return matchTexto && matchCategoria;
  });
});

const totalPages = computed(() => Math.max(1, Math.ceil(extensionesFiltradas.value.length / itemsPerPage)));

const extensionesPaginadas = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return extensionesFiltradas.value.slice(start, start + itemsPerPage);
});

watch([filtroTexto, filtroCategoria], () => {
  currentPage.value = 1;
});

const irPagina = (pagina) => {
  if (pagina >= 1 && pagina <= totalPages.value) {
    currentPage.value = pagina;
  }
};

const totalOficinas = computed(() => {
  return pbxData.filter((ext) => ext.cargo.includes("Oficina")).length;
});

const totalAdministrativos = computed(() => {
  return pbxData.filter(
    (ext) =>
      ext.cargo.includes("gerencia") ||
      ext.cargo.includes("Cartera") ||
      ext.cargo.includes("Contabilidad") ||
      ext.cargo.includes("Sistemas")
  ).length;
});

const getCategoriaClass = (cargo) => {
  if (cargo.includes("Administrador")) return "admin";
  if (cargo.includes("Retencion")) return "retencion";
  if (cargo.includes("Caja")) return "caja";
  if (cargo.includes("Cartera")) return "cartera";
  if (cargo.includes("Contabilidad")) return "contabilidad";
  if (cargo.includes("Sistemas")) return "sistemas";
  if (cargo.includes("gerencia") || cargo.includes("Subgerencia")) return "gerencia";
  return "default";
};
</script>

<style scoped>
.layout-extensiones {
  max-width: 1200px;
  margin: 0 auto;
  padding: 48px 40px;
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(6px); }
  to { opacity: 1; transform: translateY(0); }
}

.page-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 28px;
  gap: 20px;
}

.page-title {
  font-family: var(--font-heading);
  font-size: 40px;
  font-weight: 700;
  letter-spacing: -0.02em;
  color: var(--warm-dark);
  margin: 0 0 6px;
  line-height: 1.1;
}

.page-desc {
  font-size: 18px;
  color: var(--warm-gray);
  margin: 0;
}

/* Filter Bar */
.filter-bar {
  display: flex;
  gap: 16px;
  margin-bottom: 24px;
  background: var(--cream);
  padding: 20px 24px;
  border-radius: 12px;
  border: 1px solid var(--warm-border);
}

.search-wrap {
  flex: 1;
  position: relative;
  display: flex;
  align-items: center;
}

.search-icon {
  position: absolute;
  left: 14px;
  font-size: 22px;
  color: var(--warm-gray-light);
  pointer-events: none;
}

.search-wrap input {
  width: 100%;
  padding: 12px 14px 12px 48px;
  font-size: 16px;
  font-family: var(--font-body);
  background: var(--surface);
  border: 1px solid var(--warm-border);
  border-radius: 8px;
  color: var(--warm-dark);
  outline: none;
  transition: all 0.2s;
}

.search-wrap input:focus {
  border-color: var(--teal);
  box-shadow: 0 0 0 3px rgba(0, 61, 155, 0.12);
}

.search-wrap input::placeholder {
  color: var(--warm-gray-light);
}

.filter-selects {
  display: flex;
  gap: 12px;
}

.select {
  padding: 12px 36px 12px 14px;
  border: 1px solid var(--warm-border);
  border-radius: 8px;
  font-size: 16px;
  font-family: var(--font-body);
  color: var(--warm-dark);
  background: var(--surface) url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%23737685' d='M10.293 3.293L6 7.586 1.707 3.293A1 1 0 00.293 4.707l5 5a1 1 0 001.414 0l5-5a1 1 0 10-1.414-1.414z'/%3E%3C/svg%3E") no-repeat right 12px center;
  cursor: pointer;
  outline: none;
  transition: all 0.2s;
  appearance: none;
  min-width: 180px;
}

.select:focus {
  border-color: var(--teal);
  box-shadow: 0 0 0 3px rgba(0, 61, 155, 0.12);
}

/* Stats */
.stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
  margin-bottom: 28px;
}

.stat-card {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px;
  background: var(--surface);
  border: 1px solid var(--warm-border);
  border-radius: 12px;
  transition: all 0.2s;
}

.stat-card:hover {
  border-color: var(--teal);
  transform: translateY(-2px);
  box-shadow: var(--shadow-sm);
}

.stat-icon {
  font-size: 28px;
  color: var(--teal);
  width: 52px;
  height: 52px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--cream);
  border-radius: 10px;
  flex-shrink: 0;
}

.stat-card div {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.stat-label {
  font-size: 13px;
  font-weight: 600;
  color: var(--warm-gray);
}

.stat-val {
  font-size: 26px;
  font-weight: 700;
  color: var(--warm-dark);
}

/* Table */
.table-container {
  background: var(--surface);
  border: 1px solid var(--warm-border);
  border-radius: 12px;
  overflow: hidden;
  box-shadow: var(--shadow-sm);
}

.table-head {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 32px;
  border-bottom: 1px solid var(--warm-border);
  background: var(--surface);
}

.table-head h2 {
  font-family: var(--font-heading);
  font-size: 24px;
  font-weight: 600;
  color: var(--warm-dark);
  margin: 0;
}

.count-badge {
  background: var(--cream-dark);
  color: var(--warm-gray);
  padding: 4px 14px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}

.table-scroll {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--cream);
}

th {
  padding: 14px 32px 14px 32px;
  text-align: left;
  font-size: 12px;
  font-weight: 700;
  color: var(--warm-gray);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

th:first-child {
  padding-left: 32px;
}

tbody tr:nth-child(even) {
  background: rgba(232, 237, 255, 0.3);
}

tbody tr:hover {
  background: rgba(0, 61, 155, 0.04);
}

td {
  padding: 18px 12px;
  font-size: 16px;
  color: var(--warm-dark);
  border-bottom: 1px solid var(--warm-border);
  vertical-align: middle;
}

td:first-child {
  padding-left: 32px;
}

.cell-name {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  font-weight: 500;
}

.cell-icon {
  font-size: 22px;
  color: var(--teal);
}

.role-badge {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}

.role-badge.admin {
  background: rgba(0, 61, 155, 0.12);
  color: var(--teal);
}

.role-badge.retencion {
  background: rgba(0, 75, 89, 0.12);
  color: var(--terracotta);
}

.role-badge.caja {
  background: rgba(16, 185, 129, 0.12);
  color: #059669;
}

.role-badge.cartera {
  background: rgba(217, 119, 6, 0.12);
  color: #D97706;
}

.role-badge.contabilidad {
  background: rgba(124, 58, 237, 0.12);
  color: #7C3AED;
}

.role-badge.sistemas {
  background: rgba(2, 132, 199, 0.12);
  color: #0284C7;
}

.role-badge.gerencia {
  background: rgba(220, 38, 38, 0.12);
  color: #DC2626;
}

.role-badge.default {
  background: rgba(107, 114, 128, 0.12);
  color: var(--warm-gray);
}

.ext-badge {
  display: inline-block;
  padding: 6px 14px;
  background: var(--teal);
  color: white;
  border-radius: 20px;
  font-weight: 700;
  font-size: 14px;
  font-family: 'DM Sans', monospace;
}

/* Table Footer / Pagination */
.table-foot {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 32px;
  border-top: 1px solid var(--warm-border);
  background: var(--surface);
}

.foot-info {
  font-size: 12px;
  font-weight: 500;
  color: var(--warm-gray);
}

.pagination {
  display: flex;
  gap: 6px;
}

.page-btn {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--warm-border);
  border-radius: 8px;
  background: transparent;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  font-family: var(--font-body);
  color: var(--warm-dark);
  transition: all 0.2s;
}

.page-btn:hover:not(:disabled):not(.active) {
  background: var(--cream);
}

.page-btn.active {
  background: var(--teal);
  color: white;
  border-color: var(--teal);
}

.page-btn:disabled {
  opacity: 0.4;
  cursor: default;
}

.page-btn .material-symbols-outlined {
  font-size: 18px;
}

/* Empty state */
.empty {
  text-align: center;
  padding: 64px 20px;
}

.empty .material-symbols-outlined {
  font-size: 40px;
  color: var(--warm-gray-light);
  margin-bottom: 12px;
}

.empty h3 {
  font-size: 18px;
  font-weight: 600;
  color: var(--warm-dark);
  margin-bottom: 6px;
}

.empty p {
  font-size: 14px;
  color: var(--warm-gray);
}

@media (max-width: 1024px) {
  .layout-extensiones { padding: 32px 28px; }
  .page-title { font-size: 36px; }
}

@media (max-width: 768px) {
  .layout-extensiones { padding: 24px 16px; }
  .page-head { flex-direction: column; }
  .page-title { font-size: 32px; }
  .page-desc { font-size: 16px; }

  .filter-bar {
    flex-direction: column;
    padding: 16px;
  }

  .filter-selects { flex-direction: column; }
  .select { min-width: auto; }
  .stats { grid-template-columns: 1fr; }

  table { min-width: 720px; }

  .table-foot { flex-direction: column; gap: 12px; }
}

@media (max-width: 480px) {
  .layout-extensiones { padding: 20px 12px; }
  .page-title { font-size: 28px; }
  .page-desc { font-size: 15px; }
  .filter-bar { padding: 12px; gap: 10px; }
  .search-wrap input { font-size: 15px; padding: 10px 14px 10px 44px; }
  .select { font-size: 15px; padding: 10px 32px 10px 12px; }
  td { font-size: 15px; padding: 14px 8px; }
  .table-head, .table-foot { padding: 14px 20px; }
  th { padding: 12px 20px; }
  td:first-child { padding-left: 20px; }
  th:first-child { padding-left: 20px; }
  .stat-card { padding: 16px; }
  .stat-val { font-size: 22px; }
  .empty { padding: 40px 16px; }
  .foot-info { font-size: 11px; }
}
</style>
