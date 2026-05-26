<template>
  <div class="layout-formularios">
    <div class="page-head">
      <div>
        <h1 class="page-title">Formularios de Ventas</h1>
        <p class="page-desc">Accede a los formularios de ventas de cada oficina</p>
      </div>
    </div>

    <div class="filter-bar">
      <div class="search-wrap">
        <span class="material-symbols-outlined search-icon">search</span>
        <input v-model="filtroTexto" type="text" placeholder="Buscar por oficina..." />
      </div>
    </div>

    <div class="stats">
      <div class="stat-card">
        <span class="material-symbols-outlined stat-icon">description</span>
        <div>
          <span class="stat-label">Total Formularios</span>
          <span class="stat-val">{{ formulariosFiltrados.length }}</span>
        </div>
      </div>
      <div class="stat-card">
        <span class="material-symbols-outlined stat-icon">open_in_new</span>
        <div>
          <span class="stat-label">Enlaces activos</span>
          <span class="stat-val">{{ formulariosFiltrados.length }}</span>
        </div>
      </div>
    </div>

    <div class="table-container">
      <div class="table-head">
        <h2>Formularios de ventas</h2>
        <span class="count-badge">{{ formulariosFiltrados.length }} registros</span>
      </div>
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th>Formulario</th>
              <th>Acci&oacute;n</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(formulario, index) in formulariosPaginados" :key="index">
              <td>
                <div class="cell-name">
                  <span class="material-symbols-outlined cell-icon">description</span>
                  <span>{{ formulario.nombre_formulario }}</span>
                </div>
              </td>
              <td>
                <a class="btn-link" :href="formulario.enlace_formulario" target="_blank" rel="noopener noreferrer">
                  Abrir
                  <span class="material-symbols-outlined">open_in_new</span>
                </a>
              </td>
            </tr>
          </tbody>
        </table>
        <div v-if="formulariosFiltrados.length === 0" class="empty">
          <span class="material-symbols-outlined">search_off</span>
          <h3>No se encontraron formularios</h3>
          <p>Intenta ajustar los filtros de b&uacute;squeda</p>
        </div>
      </div>
      <div class="table-foot">
        <span class="foot-info" v-if="formulariosFiltrados.length > 0">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} a {{ Math.min(currentPage * itemsPerPage, formulariosFiltrados.length) }} de {{ formulariosFiltrados.length }} registros
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
const currentPage = ref(1);
const itemsPerPage = 6;

const formulariosData = [
  { nombre_formulario: "Formulario de ventas Comuneros", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSdVANncsap2oOAAaN86OD_zlejuOY1n5VN5739H5Qqpbq1Png/viewform" },
  { nombre_formulario: "Formulario de ventas Rio Cauca", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLScbDDd-6MJbAVd4S9MFLWsoZhJfa2W1Rc6ZRIQWf8bImoT7aQ/viewform" },
  { nombre_formulario: "Formulario de ventas Marroquin", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLScbb4OK4tuKYc7B_EW1nK3vPYTTqqEZVTmAWSww2n0FvXtNew/viewform" },
  { nombre_formulario: "Formulario de ventas Ceibas", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSetAURPhFQnpJWg8fbU894ZeVvGUmOXmDbhaazeSOS_kPBSkQ/viewform" },
  { nombre_formulario: "Formulario de ventas Cordoba Reservado - Antonio Nari&ntilde;o", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSc6FRvX9jXrpmh8HOzUUcR9h3pqjAVVi0iAMLgLb1nsckM2zA/viewform" },
  { nombre_formulario: "Formulario de ventas Poblado", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSdczG_eYkGnkVQeAbD6yqEayA1xf6J06_DrETVwKx8fM7Vryg/viewform" },
  { nombre_formulario: "Formulario de ventas Villa Nueva", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSfOjhU9_vcKWzg6hWCxvBr1T8Kldp9Ar8xXO1Wzq97I3ZrPvQ/viewform" },
  { nombre_formulario: "Formulario de ventas Chorros - Melendez", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSf9s2z5CNpdAeyZJYi4av827rYU8VXz6iSPc8fT_DjGZmciMQ/viewform" },
  { nombre_formulario: "Formulario de ventas Montebello", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSflyRoGp8ZViYMdMV8cMGNiRpSybZ2lBChPY5j95tYDXYvYug/viewform" },
  { nombre_formulario: "Formulario de ventas Siloe", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSeVv5Xct_WuQUPXkaXAZjFolJuwLCw4JIP0b7XPRXtW8VCNpw/viewform" },
  { nombre_formulario: "Formulario de ventas Florida", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSdDsypy6ANvhb7f_leAsGVK_VNIX9v3mNtvBOCGhJF0TuBKEw/viewform" },
  { nombre_formulario: "Formulario de ventas Pradera", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSdytMnOmKLFoNpFUk-dnFwozxw4TbGzVGwcOjjXSpDs4Wctbw/viewform" },
  { nombre_formulario: "Formulario de ventas Tulua - Andalucia - Cerrito", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSf5cR6dwwfNPxNdOHqwvZgTLWCESaaLspF5aNDX8qnlPlDRYg/viewform" },
  { nombre_formulario: "Formulario de ventas Tarqui - Huila - Altamira", enlace_formulario: "https://docs.google.com/forms/d/e/1FAIpQLSfwvswIcLy8qZSX8qw32D1p5yTgUwsXXbj4Gr-VKi8BvZfPmQ/viewform" },
];

const formulariosFiltrados = computed(() => {
  return formulariosData.filter((formulario) => {
    return !filtroTexto.value ||
      formulario.nombre_formulario.toLowerCase().includes(filtroTexto.value.toLowerCase());
  });
});

const totalPages = computed(() => Math.max(1, Math.ceil(formulariosFiltrados.value.length / itemsPerPage)));

const formulariosPaginados = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return formulariosFiltrados.value.slice(start, start + itemsPerPage);
});

watch(filtroTexto, () => {
  currentPage.value = 1;
});

const irPagina = (pagina) => {
  if (pagina >= 1 && pagina <= totalPages.value) {
    currentPage.value = pagina;
  }
};
</script>

<style scoped>
.layout-formularios {
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

th:last-child {
  padding-right: 32px;
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

.btn-link {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 10px 20px;
  background: var(--teal);
  color: white;
  text-decoration: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.2s;
  font-family: var(--font-body);
}

.btn-link:hover {
  background: var(--teal-dark);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(0, 61, 155, 0.25);
}

.btn-link .material-symbols-outlined {
  font-size: 16px;
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
  .layout-formularios { padding: 32px 28px; }
  .page-title { font-size: 36px; }
}

@media (max-width: 768px) {
  .layout-formularios { padding: 24px 16px; }
  .page-head { flex-direction: column; }
  .page-title { font-size: 32px; }
  .page-desc { font-size: 16px; }

  .filter-bar {
    flex-direction: column;
    padding: 16px;
  }

  .stats { grid-template-columns: 1fr; }

  table { min-width: 720px; }

  .table-foot { flex-direction: column; gap: 12px; }
}

@media (max-width: 480px) {
  .layout-formularios { padding: 20px 12px; }
  .page-title { font-size: 28px; }
  .page-desc { font-size: 15px; }
  .filter-bar { padding: 12px; gap: 10px; }
  .search-wrap input { font-size: 15px; padding: 10px 14px 10px 44px; }
  td { font-size: 15px; padding: 14px 8px; }
  .table-head, .table-foot { padding: 14px 20px; }
  th { padding: 12px 20px; }
  td:first-child { padding-left: 20px; }
  th:first-child { padding-left: 20px; }
  .stat-card { padding: 16px; }
  .stat-val { font-size: 22px; }
  .btn-link { padding: 8px 14px; font-size: 13px; }
  .empty { padding: 40px 16px; }
  .foot-info { font-size: 11px; }
}
</style>
