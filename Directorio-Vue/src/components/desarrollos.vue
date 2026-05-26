<template>
  <div class="layout-desarrollos">
    <div class="page-head">
      <div>
        <h1 class="page-title">Registro de Desarrollos</h1>
        <p class="page-desc">Gestiona y consulta la informaci&oacute;n de todos los desarrollos.</p>
      </div>
      <button class="btn-primary" @click="abrirModalNuevoDesarrollo">
        <span class="material-symbols-outlined">add_circle</span>
        <span>A&ntilde;adir Desarrollo</span>
      </button>
    </div>

    <div class="filter-bar">
      <div class="search-wrap">
        <span class="material-symbols-outlined search-icon">search</span>
        <input v-model="filtroTexto" type="text" placeholder="Buscar desarrollos, direcciones, encargados..." />
      </div>
    </div>

    <div class="table-container">
      <div class="table-head">
        <h2>Desarrollos Registrados</h2>
        <span class="count-badge">{{ desarrollosFiltrados.length }} desarrollos</span>
      </div>
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th>Desarrollo</th>
              <th>Tel&eacute;fono</th>
              <th>Direcci&oacute;n</th>
              <th>Encargado</th>
              <th>Ciudad</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(desarrollo, index) in desarrollosPaginados" :key="index">
              <td>
                <div class="cell-name">
                  <span class="material-symbols-outlined cell-icon">language</span>
                  <span>{{ desarrollo.nombre_desarrollo }}</span>
                </div>
              </td>
              <td><span class="cell-ext">{{ desarrollo.telefono_desarrollo }}</span></td>
              <td class="cell-addr">{{ desarrollo.direccion_desarrollo }}</td>
              <td>{{ desarrollo.Encargado_desarrollo }}</td>
              <td><span class="city-badge">{{ desarrollo.ciudad_desarrollo }}</span></td>
              <td class="cell-actions">
                <button @click="verDetalles(desarrollo)" class="btn-outline">Ver Detalles</button>
              </td>
            </tr>
          </tbody>
        </table>
        <div v-if="desarrollosFiltrados.length === 0" class="empty">
          <span class="material-symbols-outlined">search_off</span>
          <h3>No se encontraron desarrollos</h3>
          <p>Intenta ajustar los filtros de b&uacute;squeda</p>
        </div>
      </div>
      <div class="table-foot">
        <span class="foot-info" v-if="desarrollosFiltrados.length > 0">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} a {{ Math.min(currentPage * itemsPerPage, desarrollosFiltrados.length) }} de {{ desarrollosFiltrados.length }} desarrollos
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
import { useRouter } from "vue-router";

const router = useRouter();
const filtroTexto = ref("");
const currentPage = ref(1);
const itemsPerPage = 6;

const desarrollosData = [
  { nombre_desarrollo: "Desarrollo Andres Sanin", direccion_desarrollo: "CR 14 74 04 - Andres Sanin", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Jhon Breyman Loboa Villaquiran", telefono_desarrollo: "3153238184", barrios_desarrollo: ["Andres Sanin", "Puerto Mallarino"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$75.000" }, { nombre_plan: "200 Megas", precio_plan: "$85.000" }], correo_desarrollo: "telecableandressanin@gmail.com" },
  { nombre_desarrollo: "Desarrollo Ciudad del Campo", direccion_desarrollo: "CL 103 16 122 LOCAL 1 - Ciudad del Campo", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Jennifer Cerezo", telefono_desarrollo: "3172964236", barrios_desarrollo: ["Ciudad del Campo"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$50.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "400 Megas", precio_plan: "$90.000" }], correo_desarrollo: "ciudad.campo@cablecauca.com" },
  { nombre_desarrollo: "Desarrollo Arce Ciudad Cordoba", direccion_desarrollo: "CR 46 49 38 - Ciudad Cordoba", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Andres Alberto Arce Rojas", telefono_desarrollo: "3126829740", barrios_desarrollo: ["Ciudad Cordoba"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$50.000" }, { nombre_plan: "300 Megas", precio_plan: "$70.000" }, { nombre_plan: "600 Megas", precio_plan: "$95.000" }], correo_desarrollo: "arcenet945@gmail.com" },
  { nombre_desarrollo: "Desarrollo Jorge Herrera", direccion_desarrollo: "CL 86 26B 82 - Puertas del Sol 1", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Jorge Enrique Herrera Peña", telefono_desarrollo: "3137475842", barrios_desarrollo: ["Puertas del Sol 1", "San Marcos", "Tercer Milenio", "Ulpiano Lloreda"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$55.000" }, { nombre_plan: "300 Megas", precio_plan: "$75.000" }, { nombre_plan: "600 Megas", precio_plan: "$95.000" }], correo_desarrollo: "jorge.herrera.jjk@hotmail.com" },
  { nombre_desarrollo: "Desarrollo Charco Azul", direccion_desarrollo: "CL 73 25U 34 - Charco Azul", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Andres Julian Cerezo Villaquiran", telefono_desarrollo: "3225958868", barrios_desarrollo: ["Charco Azul", "Lleras Restrepo", "Sector Vivero", "7 De agosto parcial"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "menar-799@hotmail.com" },
  { nombre_desarrollo: "Desarrollo Manuela Beltran", direccion_desarrollo: "CR 27A 105 150 LOCAL 03 - Manuela Beltran", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Cristian Largache", telefono_desarrollo: "3158024117", barrios_desarrollo: ["Manuela Beltran"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "telecablemanuela@gmail.com" },
  { nombre_desarrollo: "Desarrollo Potrero Grande", direccion_desarrollo: "CR 28D 121 BIS 97 SECTOR 8 ESQUINA - Potrero Grande", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Cristian Largache - Olmedo Cerezo", telefono_desarrollo: "3157940974", barrios_desarrollo: ["Potrero Grande"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "oficinapotrerogrande@gmail.com" },
  { nombre_desarrollo: "Desarrollo El Retiro", direccion_desarrollo: "CL 52 39D 71 - El Retiro", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Hermes Noguera", telefono_desarrollo: "3002917947", barrios_desarrollo: ["El Retiro", "Vallado", "El Valladito"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "retirogpon@gmail.com" },
  { nombre_desarrollo: "Desarrollo Villa Luz", direccion_desarrollo: "CR 28D1 120A 51 - Pizamos 3", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Roberto Carlos Jimenez Alvarez", telefono_desarrollo: "3161315052", barrios_desarrollo: ["Pizamos 3", "Villa Luz", "Villa Mercado", "Asentamiento las Vegas y Lideres"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "300 Megas", precio_plan: "$70.000" }], correo_desarrollo: "telecablevillaluz@gmail.com" },
  { nombre_desarrollo: "Desarrollo Floralia", direccion_desarrollo: "CL 73 3AN 08 - Floralia", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Jhonny Cobo", telefono_desarrollo: "3178734860", barrios_desarrollo: ["Floralia"], planes_desarrollo: [{ nombre_plan: "50 Megas", precio_plan: "$50.000" }, { nombre_plan: "100 Megas", precio_plan: "$60.000" }, { nombre_plan: "300 Megas", precio_plan: "$70.000" }], correo_desarrollo: "telecablevillaluz@gmail.com" },
  { nombre_desarrollo: "Desarrollo Talanga", direccion_desarrollo: "CR 24G 86 01 - Talanga", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Lidia Karina Mariño", telefono_desarrollo: "3213759386", barrios_desarrollo: ["Talanga 1", "Talanga 2", "Compartir"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$50.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "talanga.aak@gmail.com" },
  { nombre_desarrollo: "Desarrollo Valle Grande", direccion_desarrollo: "CL 82 23 113 - Valle Grande", ciudad_desarrollo: "Cali", departamento_desarrollo: "Valle del cauca", Encargado_desarrollo: "Lidia Karina Mariño", telefono_desarrollo: "3233727269", barrios_desarrollo: ["Valle Grande"], planes_desarrollo: [{ nombre_plan: "100 Megas", precio_plan: "$50.000" }, { nombre_plan: "200 Megas", precio_plan: "$70.000" }, { nombre_plan: "300 Megas", precio_plan: "$80.000" }], correo_desarrollo: "fibravisiontv@gmail.com" },
];

const desarrollosFiltrados = computed(() => {
  return desarrollosData.filter((desarrollo) => {
    const matchTexto =
      !filtroTexto.value ||
      desarrollo.nombre_desarrollo.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      desarrollo.direccion_desarrollo.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      desarrollo.Encargado_desarrollo.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      desarrollo.telefono_desarrollo.toLowerCase().includes(filtroTexto.value.toLowerCase());
    return matchTexto;
  });
});

const totalPages = computed(() => Math.max(1, Math.ceil(desarrollosFiltrados.value.length / itemsPerPage)));

const desarrollosPaginados = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return desarrollosFiltrados.value.slice(start, start + itemsPerPage);
});

watch(filtroTexto, () => {
  currentPage.value = 1;
});

const irPagina = (pagina) => {
  if (pagina >= 1 && pagina <= totalPages.value) {
    currentPage.value = pagina;
  }
};

const verDetalles = (desarrollo) => {
  localStorage.setItem("desarrolloSeleccionado", JSON.stringify(desarrollo));
  router.push("/home/desarrollo-detalle");
};

const abrirModalNuevoDesarrollo = () => {
  console.log("Abrir modal para nuevo desarrollo");
};
</script>

<style scoped>
.layout-desarrollos {
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

.btn-primary {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  background: var(--teal);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.01em;
  cursor: pointer;
  transition: all 0.2s;
  font-family: var(--font-body);
  white-space: nowrap;
  box-shadow: 0 4px 12px rgba(0, 61, 155, 0.2);
}

.btn-primary:hover {
  background: var(--teal-light);
  transform: translateY(-1px);
  box-shadow: 0 6px 16px rgba(0, 61, 155, 0.3);
}

.btn-primary:active {
  transform: scale(0.97);
}

.btn-primary .material-symbols-outlined {
  font-size: 20px;
}

/* Filter Bar */
.filter-bar {
  display: flex;
  gap: 16px;
  margin-bottom: 28px;
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

th:last-child {
  padding-right: 32px;
  text-align: right;
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

td:last-child {
  padding-right: 32px;
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

.cell-ext {
  font-weight: 700;
  color: var(--terracotta);
  font-size: 16px;
}

.cell-addr {
  color: var(--warm-gray);
  font-size: 15px;
  max-width: 280px;
}

.city-badge {
  display: inline-block;
  padding: 4px 12px;
  background: var(--cream);
  color: var(--warm-dark);
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}

.cell-actions {
  text-align: right;
}

.btn-outline {
  padding: 8px 18px;
  background: transparent;
  border: 1px solid var(--warm-border);
  border-radius: 8px;
  color: var(--warm-dark);
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.01em;
  cursor: pointer;
  font-family: var(--font-body);
  transition: all 0.2s;
  white-space: nowrap;
}

.btn-outline:hover {
  border-color: var(--teal);
  color: var(--teal);
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
  .layout-desarrollos { padding: 32px 28px; }
  .page-title { font-size: 36px; }
}

@media (max-width: 768px) {
  .layout-desarrollos { padding: 24px 16px; }
  .page-head { flex-direction: column; }
  .page-title { font-size: 32px; }
  .page-desc { font-size: 16px; }

  .filter-bar {
    flex-direction: column;
    padding: 16px;
  }

  .btn-primary { width: 100%; justify-content: center; }

  table { min-width: 720px; }

  .table-foot { flex-direction: column; gap: 12px; }
}

@media (max-width: 480px) {
  .layout-desarrollos { padding: 20px 12px; }
  .page-title { font-size: 28px; }
  .page-desc { font-size: 15px; }
  .filter-bar { padding: 12px; gap: 10px; }
  .search-wrap input { font-size: 15px; padding: 10px 14px 10px 44px; }
  td { font-size: 15px; padding: 14px 8px; }
  .table-head, .table-foot { padding: 14px 20px; }
  th { padding: 12px 20px; }
  td:first-child { padding-left: 20px; }
  th:first-child { padding-left: 20px; }
  .empty { padding: 40px 16px; }
  .foot-info { font-size: 11px; }
}
</style>
