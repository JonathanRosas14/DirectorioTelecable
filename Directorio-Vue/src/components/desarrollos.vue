<template>
  <div class="layout-desarrollos">
    <div class="desarrollos-header">
      <div class="header-content">
        <h1 class="page-title">Registro de Desarrollos</h1>
        <p class="page-subtitle">
          Gestiona y consulta la información de todos los desarrollos
        </p>
      </div>
      <button
        class="button-nuevo-desarrollo"
        @click="abrirModalNuevoDesarrollo"
      >
        <span class="button-icon">➕</span>
        <span>Añadir Desarrollo</span>
      </button>
    </div>

    <div class="filter-container">
      <div class="filter-group">
        <div class="filter-box">
          <span class="filter-icon">🔍</span>
          <input
            v-model="filtroTexto"
            type="text"
            class="filter-input"
            placeholder="Buscar desarrollos, direcciones, encargados..."
          />
        </div>
      </div>
    </div>

    <div class="table-card">
      <div class="table-header">
        <h2 class="table-title">Desarrollos Registrados</h2>
        <span class="table-count"
          >{{ desarrollosFiltrados.length }} desarrollos</span
        >
      </div>

      <div class="table-container">
        <table class="desarrollos-table">
          <thead>
            <tr>
              <th>Desarrollo</th>
              <th>Teléfono</th>
              <th>Dirección</th>
              <th>Encargado</th>
              <th>Ciudad</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(desarrollo, index) in desarrollosFiltrados"
              :key="index"
              class="table-row"
            >
              <td class="desarrollo-nombre">
                <div class="desarrollo-cell">
                  <span class="desarrollo-icon">💻</span>
                  <span>{{ desarrollo.nombre_desarrollo }}</span>
                </div>
              </td>
              <td class="telefono">{{ desarrollo.telefono_desarrollo }}</td>
              <td class="direccion">{{ desarrollo.direccion_desarrollo }}</td>
              <td class="encargado">{{ desarrollo.Encargado_desarrollo }}</td>
              <td>
                <span class="badge-ciudad">{{
                  desarrollo.ciudad_desarrollo
                }}</span>
              </td>
              <td>
                <button @click="verDetalles(desarrollo)" class="view-button">
                  <span>Ver Detalles</span>
                </button>
              </td>
            </tr>
          </tbody>
        </table>

        <div v-if="desarrollosFiltrados.length === 0" class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>No se encontraron desarrollos</h3>
          <p>Intenta ajustar los filtros de búsqueda</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const filtroTexto = ref("");

const desarrollosData = [
  {
    nombre_desarrollo: "Desarrollo Andres Sanin",
    direccion_desarrollo: "CR 14 74 04 - Andres Sanin",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Jhon Breyman Loboa Villaquiran",
    telefono_desarrollo: "3153238184",
    barrios_desarrollo: ["Andres Sanin", "Puerto Mallarino"],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$75.000" },
      { nombre_plan: "200 Megas", precio_plan: "$85.000" },
    ],
    correo_desarrollo: "telecableandressanin@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Ciudad del Campo",
    direccion_desarrollo: "CL 103 16 122 LOCAL 1 - Ciudad del Campo",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Jennifer Cerezo",
    telefono_desarrollo: "3172964236",
    barrios_desarrollo: ["Ciudad del Campo"],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$50.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "400 Megas", precio_plan: "$90.000" },
    ],
    correo_desarrollo: "ciudad.campo@cablecauca.com",
  },
  {
    nombre_desarrollo: "Desarrollo Arce Ciudad Cordoba",
    direccion_desarrollo: "CR 46 49 38 - Ciudad Cordoba",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Andres Alberto Arce Rojas",
    telefono_desarrollo: "3126829740",
    barrios_desarrollo: ["Ciudad Cordoba"],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$50.000" },
      { nombre_plan: "300 Megas", precio_plan: "$70.000" },
      { nombre_plan: "600 Megas", precio_plan: "$95.000" },
    ],
    correo_desarrollo: "arcenet945@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Jorge Herrera",
    direccion_desarrollo: "CL 86 26B 82 - Puertas del Sol 1",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Jorge Enrique Herrera Peña",
    telefono_desarrollo: "3137475842",
    barrios_desarrollo: [
      "Puertas del Sol 1",
      "San Marcos",
      "Tercer Milenio",
      "Ulpiano Lloreda",
    ],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$55.000" },
      { nombre_plan: "300 Megas", precio_plan: "$75.000" },
      { nombre_plan: "600 Megas", precio_plan: "$95.000" },
    ],
    correo_desarrollo: "jorge.herrera.jjk@hotmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Charco Azul",
    direccion_desarrollo: "CL 73 25U 34 - Charco Azul",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Andres Julian Cerezo Villaquiran",
    telefono_desarrollo: "3225958868",
    barrios_desarrollo: [
      "Charco Azul",
      "Lleras Restrepo",
      "Sector Vivero",
      "7 De agosto parcial",
    ],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "menar-799@hotmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Manuela Beltran",
    direccion_desarrollo: "CR 27A 105 150 LOCAL 03 - Manuela Beltran",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Cristian Largache",
    telefono_desarrollo: "3158024117",
    barrios_desarrollo: ["Manuela Beltran"],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "telecablemanuela@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Potrero Grande",
    direccion_desarrollo: "CR 28D 121 BIS 97 SECTOR 8 ESQUINA - Potrero Grande",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Cristian Largache - Olmedo Cerezo",
    telefono_desarrollo: "3157940974",
    barrios_desarrollo: ["Potrero Grande"],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "oficinapotrerogrande@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo El Retiro",
    direccion_desarrollo: "CL 52 39D 71 - El Retiro",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Hermes Noguera",
    telefono_desarrollo: "3002917947",
    barrios_desarrollo: ["El Retiro", "Vallado", "El Valladito"],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "retirogpon@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Villa Luz",
    direccion_desarrollo: "CR 28D1 120A 51 - Pizamos 3",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Roberto Carlos Jimenez Alvarez",
    telefono_desarrollo: "3161315052",
    barrios_desarrollo: [
      "Pizamos 3",
      "Villa Luz",
      "Villa Mercado",
      "Asentamiento las Vegas y Lideres",
    ],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "300 Megas", precio_plan: "$70.000" },
    ],
    correo_desarrollo: "telecablevillaluz@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Floralia",
    direccion_desarrollo: "CL 73 3AN 08 - Floralia",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Jhonny Cobo",
    telefono_desarrollo: "3178734860",
    barrios_desarrollo: ["Floralia"],
    planes_desarrollo: [
      { nombre_plan: "50 Megas", precio_plan: "$50.000" },
      { nombre_plan: "100 Megas", precio_plan: "$60.000" },
      { nombre_plan: "300 Megas", precio_plan: "$70.000" },
    ],
    correo_desarrollo: "telecablevillaluz@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Talanga",
    direccion_desarrollo: "CR 24G 86 01 - Talanga",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Lidia Karina Mariño",
    telefono_desarrollo: "3213759386",
    barrios_desarrollo: ["Talanga 1", "Talanga 2", "Compartir"],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$50.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "talanga.aak@gmail.com",
  },
  {
    nombre_desarrollo: "Desarrollo Valle Grande",
    direccion_desarrollo: "CL 82 23 113 - Valle Grande",
    ciudad_desarrollo: "Cali",
    departamento_desarrollo: "Valle del cauca",
    Encargado_desarrollo: "Lidia Karina Mariño",
    telefono_desarrollo: "3233727269",
    barrios_desarrollo: ["Valle Grande"],
    planes_desarrollo: [
      { nombre_plan: "100 Megas", precio_plan: "$50.000" },
      { nombre_plan: "200 Megas", precio_plan: "$70.000" },
      { nombre_plan: "300 Megas", precio_plan: "$80.000" },
    ],
    correo_desarrollo: "fibravisiontv@gmail.com",
  },
];

const desarrollosFiltrados = computed(() => {
  return desarrollosData.filter((desarrollo) => {
    const matchTexto =
      !filtroTexto.value ||
      desarrollo.nombre_desarrollo
        .toLowerCase()
        .includes(filtroTexto.value.toLowerCase()) ||
      desarrollo.direccion_desarrollo
        .toLowerCase()
        .includes(filtroTexto.value.toLowerCase()) ||
      desarrollo.Encargado_desarrollo.toLowerCase().includes(
        filtroTexto.value.toLowerCase()
      ) ||
      desarrollo.telefono_desarrollo
        .toLowerCase()
        .includes(filtroTexto.value.toLowerCase());

    return matchTexto;
  });
});

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
  padding: 30px 40px;
  background: linear-gradient(to bottom, #f8f9fa 0%, #e9ecef 100%);
  min-height: calc(100vh - 90px);
}

.desarrollos-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  flex-wrap: wrap;
  gap: 20px;
}

.header-content {
  flex: 1;
}

.page-title {
  font-size: 32px;
  font-weight: 700;
  color: #1e3c72;
  margin: 0 0 8px 0;
}

.page-subtitle {
  font-size: 16px;
  color: #64748b;
  margin: 0;
}

.button-nuevo-desarrollo {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 14px 28px;
  background: linear-gradient(135deg, #2a5298 0%, #1e3c72 100%);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  box-shadow: 0 4px 16px rgba(42, 82, 152, 0.3);
  transition: all 0.3s ease;
}

.button-nuevo-desarrollo:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 24px rgba(42, 82, 152, 0.4);
}

.button-icon {
  font-size: 18px;
}

.filter-container {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.filter-group {
  display: flex;
  gap: 16px;
  flex-wrap: wrap;
  flex: 1;
}

.filter-box {
  flex: 1;
  min-width: 300px;
  position: relative;
  display: flex;
  align-items: center;
  background: white;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  padding: 0 16px;
  transition: all 0.3s ease;
  height: 50px;
}

.filter-box:focus-within {
  border-color: #2a5298;
  box-shadow: 0 0 0 4px rgba(42, 82, 152, 0.08);
}

.filter-icon {
  font-size: 20px;
  color: #94a3b8;
  margin-right: 12px;
}

.filter-input {
  flex: 1;
  border: none;
  outline: none;
  padding: 14px 0;
  font-size: 15px;
  color: #1e293b;
  background: transparent;
}

.filter-input::placeholder {
  color: #94a3b8;
}

.table-card {
  background: white;
  border-radius: 16px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  overflow: hidden;
}

.table-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 24px 30px;
  border-bottom: 2px solid #e2e8f0;
}

.table-title {
  font-size: 20px;
  font-weight: 700;
  color: #1e3c72;
  margin: 0;
}

.table-count {
  font-size: 14px;
  font-weight: 600;
  color: #64748b;
  background: #f1f5f9;
  padding: 6px 14px;
  border-radius: 20px;
}

.table-container {
  overflow-x: auto;
}

.desarrollos-table {
  width: 100%;
  border-collapse: collapse;
}

.desarrollos-table thead {
  background: #f8fafc;
}

.desarrollos-table th {
  padding: 16px 20px;
  text-align: left;
  font-size: 13px;
  font-weight: 700;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  border-bottom: 2px solid #e2e8f0;
}

.table-row {
  transition: all 0.2s ease;
  border-bottom: 1px solid #f1f5f9;
}

.table-row:hover {
  background: #f8fafc;
}

.desarrollos-table td {
  padding: 18px 20px;
  font-size: 15px;
  color: #334155;
}

.desarrollo-cell {
  display: flex;
  align-items: center;
  gap: 12px;
}

.desarrollo-icon {
  font-size: 20px;
}

.desarrollo-nombre {
  font-weight: 600;
  color: #1e3c72;
}

.telefono {
  font-weight: 600;
  color: #2a5298;
}

.direccion {
  color: #64748b;
  font-size: 14px;
  max-width: 300px;
}

.badge-ciudad {
  display: inline-block;
  padding: 6px 14px;
  background: linear-gradient(
    135deg,
    rgba(42, 82, 152, 0.1) 0%,
    rgba(30, 60, 114, 0.1) 100%
  );
  color: #2a5298;
  border-radius: 20px;
  font-size: 13px;
  font-weight: 600;
}

.view-button {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: white;
  border: 2px solid #e2e8f0;
  border-radius: 10px;
  color: #334155;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.view-button:hover {
  border-color: #2a5298;
  background: #f8fafc;
  color: #2a5298;
  transform: translateY(-2px);
}

.empty-state {
  text-align: center;
  padding: 80px 20px;
}

.empty-icon {
  font-size: 64px;
  margin-bottom: 20px;
  opacity: 0.5;
}

.empty-state h3 {
  font-size: 20px;
  font-weight: 700;
  color: #1e3c72;
  margin-bottom: 8px;
}

.empty-state p {
  font-size: 15px;
  color: #64748b;
}

@media (max-width: 768px) {
  .layout-desarrollos {
    padding: 20px;
  }

  .desarrollos-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .page-title {
    font-size: 24px;
  }

  .button-nuevo-desarrollo {
    width: 100%;
    justify-content: center;
  }

  .filter-container {
    flex-direction: column;
  }

  .filter-box {
    min-width: 100%;
  }

  .table-container {
    overflow-x: scroll;
  }

  .desarrollos-table {
    min-width: 800px;
  }
}
</style>
