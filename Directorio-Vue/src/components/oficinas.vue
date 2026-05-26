<template>
  <div class="layout-oficinas">
    <div class="page-head">
      <div>
        <h1 class="page-title">Registro de Oficinas</h1>
        <p class="page-desc">Gestiona y consulta la informaci&oacute;n de todas las oficinas.</p>
      </div>
      <button class="btn-primary" @click="abrirModalNuevaOficina">
        <span class="material-symbols-outlined">add_circle</span>
        <span>A&ntilde;adir Oficina</span>
      </button>
    </div>

    <div class="filter-bar">
      <div class="search-wrap">
        <span class="material-symbols-outlined search-icon">search</span>
        <input v-model="filtroTexto" type="text" placeholder="Buscar oficinas, direcciones, administradores..." />
      </div>
      <div class="filter-selects">
        <select v-model="filtroCiudad" class="select">
          <option value="Todos">Todas las ciudades</option>
          <option value="Cali">Cali</option>
          <option value="Florida">Florida</option>
          <option value="Pradera">Pradera</option>
          <option value="Tulua">Tulu&aacute;</option>
          <option value="Tarqui">Tarqui</option>
          <option value="Andalucia">Andaluc&iacute;a</option>
          <option value="Cerrito">Cerrito</option>
        </select>
        <select class="select">
          <option value="Valle del Cauca">Valle del Cauca</option>
        </select>
      </div>
    </div>

    <div class="table-container">
      <div class="table-head">
        <h2>Oficinas Registradas</h2>
        <span class="count-badge">{{ oficinasFiltradas.length }} oficinas</span>
      </div>
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th>Oficina</th>
              <th>Extensi&oacute;n</th>
              <th>Direcci&oacute;n</th>
              <th>Administrador</th>
              <th>Ciudad</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(oficina, index) in oficinasPaginadas" :key="index">
              <td>
                <div class="cell-name">
                  <span class="material-symbols-outlined cell-icon">corporate_fare</span>
                  <span>{{ oficina.nombre }}</span>
                </div>
              </td>
              <td><span class="cell-ext">{{ oficina.pbx }}</span></td>
              <td class="cell-addr">{{ oficina.direccion }}</td>
              <td>{{ oficina.administrador }}</td>
              <td><span class="city-badge">{{ oficina.ciudad }}</span></td>
              <td class="cell-actions">
                <button @click="verDetalles(oficina)" class="btn-outline">Ver Detalles</button>
              </td>
            </tr>
          </tbody>
        </table>
        <div v-if="oficinasFiltradas.length === 0" class="empty">
          <span class="material-symbols-outlined">search_off</span>
          <h3>No se encontraron oficinas</h3>
          <p>Intenta ajustar los filtros de b&uacute;squeda</p>
        </div>
      </div>
      <div class="table-foot">
        <span class="foot-info" v-if="oficinasFiltradas.length > 0">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} a {{ Math.min(currentPage * itemsPerPage, oficinasFiltradas.length) }} de {{ oficinasFiltradas.length }} oficinas
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
const filtroCiudad = ref("Todos");
const currentPage = ref(1);
const itemsPerPage = 6;

const oficinasData = [
  { nombre: "Oficina Mariano Ramos", direccion: "CR 46 40 14 LOCAL 1-09 - San Andresito del Oriente", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Sandra Balanta", pbx: "1221", telefono: "3242979487", barrios: ["Mariano Ramos", "Republica de Israel", "Brisas del Limonar"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "NO", direccion: " " }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Ceibas", direccion: "CR 7L BIS 66 05 - Ceibas", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Erika Rivas", pbx: "1125", telefono: "3145881541", barrios: ["Las ceibas", "San Marino", "Los pinos", "Cali Bella", "Alfonso Lopez I", "Fepicol", "Las veraneras"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "NO", direccion: " " }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Villa Nueva", direccion: "CL 50 28G 68 - 12 de Octubre", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Diana Carolina Dorado Guauña", pbx: "1117", telefono: "3103760868", barrios: ["12 de Octubre", "Villa del Sur", "Idenpendecia", "Conquistadores", "Eduerdado Santos", "Paraiso", "Paraiso", "Gran Colombia", "Yira Castro", "Rodeo", "Asturias", "Bello Horizonte", "San pedro"], planes: [{ nombre: "500 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Drogueria VJ", direccion: "- Dg 30 31 23 San Pedro Claver" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Poblado", direccion: "CR 28F 72J 15 - Poblado", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Maria Eugenia Diaz", pbx: "1135", telefono: "3151907600", barrios: ["Robles", "Poblado I", "Poblado II"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Punto de recaudo Zona Virtual", direccion: "" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Rio Cauca", direccion: "CL 75B 23A 81 - Centro Comercial Rio Cauca LOCAL 73", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Alejandra Giraldo", pbx: "1127", telefono: "3126436349", barrios: ["Puertas del sol", "Decepaz (Liderez Decepaz, Remansos De comfandi, Ciudadela del Rio, Manantial, Compartir)", "Manuela Beltran", "Invicali", "Torres de Rio Cauca", "Centro Comercial Rio Cauca"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Ferreteria Multicenter", direccion: "- CL 112 26B1 05" }, { nombre: "Peluqueria Marizolany", direccion: "- CL 123 26H2 19" }, { nombre: "Efecty decepaz", direccion: "- CL 120F 22 14" }, { nombre: "Zona Cell", direccion: "- CR 26D 94 122" }, { nombre: "Punto de pago Decepaz", direccion: "- CL 120I 22 49" }, { nombre: "Punto de pago Puertas del sol", direccion: "- CL 96A 26B1 101" }], personalAdicional: [{ nombre: "Jhan Paul Sarria", cargo: "Rentencion - Caja", pbx: "NO", telefono: "3161561740" }] },
  { nombre: "Oficina Cordoba Reservado", direccion: "CR 47 55A 37 - Cordoba Reservado", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Vanessa Villegas G", pbx: "1130", telefono: "3155599161", barrios: ["Cordoba Reservado", "Llano Verde", "Morichal de comfandi"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "NO", direccion: "" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Comuneros", direccion: "CL 55 29A 123 - Comuneros", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Claudia Vargas", pbx: "1112", telefono: "3243714326", barrios: ["Bonilla Aragon", "Laureano Gomez", "Comuneros I", "Mojica", "Pilar Tairona", "Unidad Portal del Parque"], planes: [{ nombre: "50 megas", precio: "$40.000 Migracion de TV" }, { nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Drogueria Nuevo Latir", direccion: "- CR 28D 80 16" }, { nombre: "Servientrega Laureano Gomez", direccion: "- CL 32A 50 13" }, { nombre: "Efecty Mojica", direccion: "- CL 72z1 28e6 81" }, { nombre: "Punto de pago Bonilla", direccion: "- CL 92 28 11" }, { nombre: " Segundo Punto de pago Bonilla ", direccion: "- CR 26P5 87 67" }], personalAdicional: [{ nombre: "Brush Zapata", cargo: "Retencion", pbx: "1212", telefono: "3178899837" }, { nombre: "Ana Yibe Fontal", cargo: "Caja", pbx: "1116", telefono: "NO" }] },
  { nombre: "Oficina Marroquin", direccion: "CR 26M2 87 04 - Marroquin 1", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Leidy Johana Ospina", pbx: "1136", telefono: "3122421002", barrios: ["Marroquin 1", "Marroquin 2", "Alirio Mora", "Los Naranjos"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Punto de pago Alirio Mora", direccion: "- CL 76 26B1 26" }, { nombre: "Miscelanea la 74", direccion: "- CR 75B 26A 28" }, { nombre: "Punto de pago Puertas del sol", direccion: "- CL 96A 26B1 101" }, { nombre: "Punto de pago Marroquin 2", direccion: "- CR 26G9 73 39" }, { nombre: "Punto de pago Marroquin 1", direccion: "- CR 25P5 87 67" }], personalAdicional: [{ nombre: "Maira Hernandez", cargo: "Retencion, caja", pbx: "1105", telefono: "3128655642" }] },
  { nombre: "Oficina Chorros", direccion: "CL 1 BIS OESTE 73D 85B - Mario Correa ", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Sharon Fuentes", pbx: "1110", telefono: "3182838808", barrios: ["Mario Correa", "Chorros", "Louders", "Prados del Sur", "La marranera", "Golositos", "Alto Napoles"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Sala de internet Marlin", direccion: "- CL 3C OESTE 90 15" }, { nombre: "Miscelanea Raquel", direccion: "- CR 94A 1 60" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Montebello", direccion: "CL 12 OESTE 42 12 - Centro Montebello ", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Jhoiner Arturo Barbosa Otalvaro", pbx: "1211", telefono: "3147773428", barrios: ["Montebello"], planes: [{ nombre: "200 megas", precio: "$50.000" }, { nombre: "350 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$80.000" }], puntosRecaudo: [{ nombre: "Tienda Kary", direccion: "- AV 47 OESTE 9A 112" }, { nombre: "Tienda Luz Mery", direccion: "- CL 4 OESTE 43A 07" }, { nombre: "Parasol Rojo", direccion: "- CL 12 OESTE 36 42" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Siloe", direccion: "CL 1 OESTE 52 370 - Belisario Siloe ", ciudad: "Cali", departamento: "Valle del cauca", administrador: "Mayely Mosquera", pbx: "1111", telefono: "3188073456", barrios: ["Siloe"], planes: [{ nombre: "20 megas", precio: "$60.000" }, { nombre: "TV", precio: "$30.000" }], puntosRecaudo: [{ nombre: "Punto de recaudo Maitte Silva", direccion: "- CL 10 OESTE 49C 40" }, { nombre: "Punto de recaudo Eimmy muñoz", direccion: "- CL 10 OESTE 50 53" }, { nombre: "Punto de recaudo Monica Valencia", direccion: "- CL 14 OESTE 48 68" }, { nombre: "Punto de recaudo Geidy Martinez", direccion: "- CL 13 531 42" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Cerrito", direccion: "CL 6 6 50 - La Estrella ", ciudad: "Cerrito", departamento: "Valle del cauca", administrador: "Angelica Hurtado Silva", pbx: "1211", telefono: "3215931208", barrios: ["Nuevo Municipio(HFC)", "Nuevo Amanecer(HFC)", "El Saman(HFC)", "Villa Lina(HFC)", "Porvenir(HFC)", "La Paz (HFC)", "Coincer(HFC)", "Los Samanes(HFC)", "Villa Del Carmen(HFC)", "Asovicons(HFC)", "Rincones(HFC)", "La Esperanza(HFC)", "Pueblito Valluno(HFC)", "Prado Valle(HFC)", "Las Orquideas(HFC)", "Buenos Aires(GPON)", "Cincuentenario(GPON)", "Lares Del Paraiso(GPON)", "La Estrella(GPON)", "San Rafael(GPON)", "Chapinero(SOLO TV)", "Santa Barbara(SOLO TV)"], planes: [{ nombre: "TV + NET 50 MB", precio: "" }, { nombre: "TV + NET 100 MB", precio: "" }, { nombre: "TV + NET 200 MB", precio: "" }, { nombre: "TV + NET 300 MB", precio: "" }, { nombre: "TV + NET 20 MB HFC", precio: "" }], puntosRecaudo: [{ nombre: "Punto de recuado Jose Rodrigo Garcia", direccion: "- CL 9 13 65" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Andalucia", direccion: "CL 12 5 49 - Centro", ciudad: "Andalucia", departamento: "Valle del cauca", administrador: "Maria José Arias Hernández", pbx: "1215", telefono: "3154106483", barrios: ["Alianza", "Altamira", "Centenario", "Centro", "Colinas", "Estacion", "Floresta 1", "Floresta 2", "Paraiso", "Reubicacion", "Sol y luna", "Retorno"], planes: [{ nombre: "20 megas", precio: "$40.000" }, { nombre: "TV", precio: "$30.000" }], puntosRecaudo: [{ nombre: "NO", direccion: "" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Tulua", direccion: "TVSAL 12 25B 56 - Bolivar ", ciudad: "Tulua", departamento: "Valle del cauca", administrador: "Maria del Carmen Morales ", pbx: "1207", telefono: "3169621818 - 3169043288", barrios: ["Saman (TV HFC)", "Portales del rio (TV HFC)", "Alameda 1 (TV HFC)", "Alameda 2 (TV HFC)", "Palmar (TV HFC)", "Internacional (TV HFC)", "Villa colombia (TV HFC)", "Municipal (TV HFC)", "Bosquesito (TV HFC)", "Jorge eliecer gaitan (TV HFC)", "Chimangos (TV HFC)", "Bello horizonte (TV HFC)", "Refugio (TV HFC)", "Diablos rojos (TV HFC)", "Flor de la campana (TV HFC)", "La esperanza (TV HFC)", "Las delicias (TV HFC)", "La ceiba (TV HFC)", "Rojas (TV HFC)", "Ruben cruz (TV HFC)", "Los olmos (TV HFC)", "Playas (TV HFC)", "Graciela (TV HFC)", "El jardin (TV HFC)", "Maracaibo (TV HFC)", "Siete de agosto (TV HFC)", "Sintra sancarlos (TV HFC)", "Portales de rio paila (TV HFC)", "Santa ines (TV HFC)", "Guayacanes (TV HFC)", "San luis (TV HFC)", "Estambul (TV HFC)", "Farfan (TV HFC)", "Nuevo farfan (TV HFC)", "Asoagrin (TV HFC)", "Limonar (TV HFC)", "San arino (TV HFC)", "Laures 1 (TV HFC)", "Laureles 2 (TV HFC)", "Villa del lago (TV HFC)", "La nieves (TV HFC)", "Veraneras (TV HFC)", "Popular (TV HFC)", "Villanueva (TV HFC)", "Santarita et2 (TV HFC)", "Moralito (TV HFC)", "Bosque de maracaibo (NET + TV HFC)", "Tercer milenio (NET + TV HFC)", "El bosque (NET + TV HFC)", "La cruz (NET + TV HFC)", "Av cali (NET + TV HFC)", "Salecianos (NET + TV HFC)", "La bastilla (NET + TV HFC)"], planes: [{ nombre: "20 megas", precio: "$40.000" }, { nombre: "TV", precio: "$30.000" }], puntosRecaudo: [{ nombre: "Punto de pago Diego Cell", direccion: "CL 13 49 20" }, { nombre: "Carlos Evandro Vanegas", direccion: "CL 12 A 28B 72" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Tarqui", direccion: "CL 3 5 61 LOCAL 2 - Antonio Ricaute", ciudad: "Tarqui", departamento: "Valle del cauca", administrador: "Claudia Patricia Jaramillo Montoya", pbx: "1104", telefono: "3204564217", barrios: ["Villas del canadá (TV HFC)", "Manuel de jesús (TV HFC)", "Villa magdalena (TV HFC)", "San antonio (TV HFC)", "Villa del rio (TV HFC)", "Antonio ricaute (TV HFC)", "Centro (TV HFC)", "La veguita (TV HFC)", "Vereda san joaquín (TV HFC)", "La loma (TV HFC)", "La bodega (TV HFC)", "Villa aurora (TV HFC)", "Ciudadela otoniel rojas correa (TV HFC)", "Hato nuevo (TV HFC)", "Minuto de dios (TV HFC)", "Portal del sur (TV HFC)", "Portal del sur 2da etapa (TV HFC)", "El estadio (TV HFC)", "El jardín (TV HFC)", "Circunvalar (TV HFC)", "Las brisas (TV HFC)", "Las brisas 2da etapa (TV HFC)", "Llano del hato o san josé obrero (TV HFC)", "Circunvalar (TV HFC)", "San joaquin (TV HFC)", "Villas del canada (NET + TV HFC)", "Antonio ricaute (NET + TV HFC)", "Las brisas (NET + TV HFC)", "Centro (NET + TV HFC)", "La loma (NET + TV HFC)", "Villa aurora (NET + TV HFC)", "Hato nuevo (NET + TV HFC)", "Minuto de dios (NET + TV HFC)", "Portal del sur 2 etapa (NET + TV HFC)"], planes: [{ nombre: "20 megas", precio: "$50.000" }, { nombre: "TV", precio: "$30.000" }], puntosRecaudo: [{ nombre: "NO", direccion: "" }], personalAdicional: [{ nombre: "NO", cargo: "NO", pbx: "", telefono: "" }] },
  { nombre: "Oficina Florida", direccion: "CL 9 16 74 - Florida", ciudad: "Florida", departamento: "Valle del cauca", administrador: "Maylhen Melo", pbx: "1107", telefono: "3188139581-3207591526", barrios: ["Florida"], planes: [{ nombre: "100 megas", precio: "$50.000" }, { nombre: "300 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$95.000" }], puntosRecaudo: [{ nombre: "Tu amigo comunicaciones", direccion: "- CL 10 12 33 San antonio" }, { nombre: "Aqui es eliza irleym", direccion: "- CR 13 7 37 La cabaña" }, { nombre: "Miscelanea@tramites", direccion: "- CL 9 21 44 La esperanza" }, { nombre: "Multiservicios", direccion: "- CR 14 5 04 La cabaña" }, { nombre: "Variedades pao", direccion: "- CR 20 13 34 San jorge" }, { nombre: "Autoservicio ja en la 10", direccion: "- CL 10 3 64" }, { nombre: "Tienda llanito", direccion: "- Llanito" }], personalAdicional: [{ nombre: "Lizeth Johanna", cargo: "Retencion", pbx: "1118", telefono: "NO" }, { nombre: "Cintya Cuaran", cargo: "Caja", pbx: "NO", telefono: "NO" }, { nombre: "Sebastian Prada", cargo: "Cartera", pbx: "1114", telefono: "NO" }] },
  { nombre: "Oficina Pradera", direccion: "CL 6 11 25 - Centro", ciudad: "Pradera", departamento: "Valle del cauca", administrador: "Mayra Alejandra Rivera", pbx: "1220", telefono: "3218117199", barrios: ["Pradera"], planes: [{ nombre: "100 megas", precio: "$50.000" }, { nombre: "300 megas", precio: "$70.000" }, { nombre: "600 megas", precio: "$95.000" }], puntosRecaudo: [{ nombre: "Papeleria Panda", direccion: "- MZ C CS 1 La Lorena" }, { nombre: "Aqui es eliza irleym", direccion: "- CL 10 8 48 San Roque " }, { nombre: "Interrapidisimo - Multiservicios", direccion: "- CL 8 13 53 Ant. Ricaute" }], personalAdicional: [{ nombre: "Jackeline Rondon", cargo: "Caja", pbx: "1108", telefono: "NO" }] },
];

const oficinasFiltradas = computed(() => {
  return oficinasData.filter((oficina) => {
    const matchTexto =
      !filtroTexto.value ||
      oficina.nombre.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      oficina.direccion.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      oficina.administrador.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      oficina.pbx.toLowerCase().includes(filtroTexto.value.toLowerCase());
    const matchCiudad = filtroCiudad.value === "Todos" || oficina.ciudad === filtroCiudad.value;
    return matchTexto && matchCiudad;
  });
});

const totalPages = computed(() => Math.max(1, Math.ceil(oficinasFiltradas.value.length / itemsPerPage)));

const oficinasPaginadas = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return oficinasFiltradas.value.slice(start, start + itemsPerPage);
});

watch([filtroTexto, filtroCiudad], () => {
  currentPage.value = 1;
});

const irPagina = (pagina) => {
  if (pagina >= 1 && pagina <= totalPages.value) {
    currentPage.value = pagina;
  }
};

const verDetalles = (oficina) => {
  localStorage.setItem('oficinaSeleccionada', JSON.stringify(oficina))
  router.push('/home/oficina-detalle')
}

const abrirModalNuevaOficina = () => {
  console.log("Abrir modal para nueva oficina");
};
</script>

<style scoped>
.layout-oficinas {
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
  min-width: 160px;
}

.select:focus {
  border-color: var(--teal);
  box-shadow: 0 0 0 3px rgba(0, 61, 155, 0.12);
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
  .layout-oficinas { padding: 32px 28px; }
  .page-title { font-size: 36px; }
}

@media (max-width: 768px) {
  .layout-oficinas { padding: 24px 16px; }
  .page-head { flex-direction: column; }
  .page-title { font-size: 32px; }
  .page-desc { font-size: 16px; }

  .filter-bar {
    flex-direction: column;
    padding: 16px;
  }

  .filter-selects { flex-direction: column; }
  .select { min-width: auto; }
  .btn-primary { width: 100%; justify-content: center; }

  table { min-width: 720px; }

  .table-foot { flex-direction: column; gap: 12px; }
}

@media (max-width: 480px) {
  .layout-oficinas { padding: 20px 12px; }
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
  .empty { padding: 40px 16px; }
  .foot-info { font-size: 11px; }
}
</style>
