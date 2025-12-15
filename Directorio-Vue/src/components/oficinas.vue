<template>
  <div class="layout-oficinas">
    <div class="oficinas-header">
      <div class="header-content">
        <h1 class="page-title">Registro de Oficinas</h1>
        <p class="page-subtitle">
          Gestiona y consulta la información de todas las oficinas
        </p>
      </div>
      <button class="button-nueva-oficina" @click="abrirModalNuevaOficina">
        <span class="button-icon">➕</span>
        <span>Añadir Oficina</span>
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
            placeholder="Buscar oficinas, direcciones, administradores..."
          />
        </div>
      </div>

      <div class="filter-group">
        <div class="filter-box-select">
          <label class="filter-label">Ciudad</label>
          <select v-model="filtroCiudad" class="filter-select">
            <option value="Todos">Todas las ciudades</option>
            <option value="Cali">Cali</option>
            <option value="Florida">Florida</option>
            <option value="Pradera">Pradera</option>
            <option value="Tulua">Tuluá</option>
            <option value="Tarqui">Tarqui</option>
            <option value="Andalucia">Andalucía</option>
            <option value="Cerrito">Cerrito</option>
          </select>
        </div>

        <div class="filter-box-select">
          <label class="filter-label">Departamento</label>
          <select class="filter-select">
            <option value="Valle del Cauca">Valle del Cauca</option>
          </select>
        </div>
      </div>
    </div>

    <div class="table-card">
      <div class="table-header">
        <h2 class="table-title">Oficinas Registradas</h2>
        <span class="table-count">{{ oficinasFiltradas.length }} oficinas</span>
      </div>

      <div class="table-container">
        <table class="oficinas-table">
          <thead>
            <tr>
              <th>Oficina</th>
              <th>Extensión</th>
              <th>Dirección</th>
              <th>Administrador</th>
              <th>Ciudad</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(oficina, index) in oficinasFiltradas"
              :key="index"
              class="table-row"
            >
              <td class="oficina-nombre">
                <div class="oficina-cell">
                  <span class="oficina-icon">🏢</span>
                  <span>{{ oficina.nombre }}</span>
                </div>
              </td>
              <td class="extension">{{ oficina.pbx }}</td>
              <td class="direccion">{{ oficina.direccion }}</td>
              <td class="administrador">{{ oficina.administrador }}</td>
              <td>  
                <span class="badge-ciudad">{{ oficina.ciudad }}</span>
              </td>
              <td>
                <button @click="verDetalles(oficina)" class="view-button">
                  <span>Ver Detalles</span>
                </button>
              </td>
            </tr>
          </tbody>
        </table>

        <div v-if="oficinasFiltradas.length === 0" class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>No se encontraron oficinas</h3>
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
const filtroCiudad = ref("Todos");

const oficinasData = [
  {
    nombre: "Oficina Mariano Ramos",
    direccion: "CR 46 40 14 LOCAL 1-09 - San Andresito del Oriente",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Sandra Balanta",
    pbx: "1221",
    telefono: "3242979487",
    barrios: ["Mariano Ramos", "Republica de Israel", "Brisas del Limonar"],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [{ nombre: "NO", direccion: " " }],
    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Ceibas",
    direccion: "CR 7L BIS 66 05 - Ceibas",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Erika Rivas",
    pbx: "1125",
    telefono: "3145881541",
    barrios: [
      "Las ceibas",
      "San Marino",
      "Los pinos",
      "Cali Bella",
      "Alfonso Lopez I",
      "Fepicol",
      "Las veraneras",
    ],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [{ nombre: "NO", direccion: " " }],
    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Villa Nueva",
    direccion: "CL 50 28G 68 - 12 de Octubre",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Diana Carolina Dorado Guauña",
    pbx: "1117",
    telefono: "3103760868",
    barrios: [
      "12 de Octubre",
      "Villa del Sur",
      "Idenpendecia",
      "Conquistadores",
      "Eduerdado Santos",
      "Paraiso",
      "Paraiso",
      "Gran Colombia",
      "Yira Castro",
      "Rodeo",
      "Asturias",
      "Bello Horizonte",
      "San pedro",
    ],
    planes: [
      { nombre: "500 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Drogueria VJ", direccion: "- Dg 30 31 23 San Pedro Claver" },
    ],
    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Poblado",
    direccion: "CR 28F 72J 15 - Poblado",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Maria Eugenia Diaz",
    pbx: "1135",
    telefono: "3151907600",
    barrios: ["Robles", "Poblado I", "Poblado II"],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [{ nombre: "Punto de recaudo Zona Virtual", direccion: "" }],
    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Rio Cauca",
    direccion: "CL 75B 23A 81 - Centro Comercial Rio Cauca LOCAL 73",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Alejandra Giraldo",
    pbx: "1127",
    telefono: "3126436349",
    barrios: [
      "Puertas del sol",
      "Decepaz (Liderez Decepaz, Remansos De comfandi, Ciudadela del Rio, Manantial, Compartir)",
      "Manuela Beltran",
      "Invicali",
      "Torres de Rio Cauca",
      "Centro Comercial Rio Cauca",
    ],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Ferreteria Multicenter", direccion: "- CL 112 26B1 05" },
      { nombre: "Peluqueria Marizolany", direccion: "- CL 123 26H2 19" },
      { nombre: "Efecty decepaz", direccion: "- CL 120F 22 14" },
      { nombre: "Zona Cell", direccion: "- CR 26D 94 122" },
      { nombre: "Punto de pago Decepaz", direccion: "- CL 120I 22 49" },
      {
        nombre: "Punto de pago Puertas del sol",
        direccion: "- CL 96A 26B1 101",
      },
    ],

    personalAdicional: [
      {
        nombre: "Jhan Paul Sarria",
        cargo: "Rentencion - Caja",
        pbx: "NO",
        telefono: "3161561740",
      },
    ],
  },
  {
    nombre: "Oficina Cordoba Reservado",
    direccion: "CR 47 55A 37 - Cordoba Reservado",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Vanessa Villegas G",
    pbx: "1130",
    telefono: "3155599161",
    barrios: ["Cordoba Reservado", "Llano Verde", "Morichal de comfandi"],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [{ nombre: "NO", direccion: "" }],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Comuneros",
    direccion: "CL 55 29A 123 - Comuneros",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Claudia Vargas",
    pbx: "1112",
    telefono: "3243714326",
    barrios: [
      "Bonilla Aragon",
      "Laureano Gomez",
      "Comuneros I",
      "Mojica",
      "Pilar Tairona",
      "Unidad Portal del Parque",
    ],
    planes: [
      { nombre: "50 megas", precio: "$40.000 Migracion de TV" },
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Drogueria Nuevo Latir", direccion: "- CR 28D 80 16" },
      { nombre: "Servientrega Laureano Gomez", direccion: "- CL 32A 50 13" },
      { nombre: "Efecty Mojica", direccion: "- CL 72z1 28e6 81" },
      { nombre: "Punto de pago Bonilla", direccion: "- CL 92 28 11" },
      {
        nombre: " Segundo Punto de pago Bonilla ",
        direccion: "- CR 26P5 87 67",
      },
    ],

    personalAdicional: [
      {
        nombre: "Brush Zapata",
        cargo: "Retencion",
        pbx: "1212",
        telefono: "3178899837",
      },
      {
        nombre: "Ana Yibe Fontal",
        cargo: "Caja",
        pbx: "1116",
        telefono: "NO",
      },
    ],
  },
  {
    nombre: "Oficina Marroquin",
    direccion: "CR 26M2 87 04 - Marroquin 1",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Leidy Johana Ospina",
    pbx: "1136",
    telefono: "3122421002",
    barrios: ["Marroquin 1", "Marroquin 2", "Alirio Mora", "Los Naranjos"],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Punto de pago Alirio Mora", direccion: "- CL 76 26B1 26" },
      { nombre: "Miscelanea la 74", direccion: "- CR 75B 26A 28" },
      {
        nombre: "Punto de pago Puertas del sol",
        direccion: "- CL 96A 26B1 101",
      },
      { nombre: "Punto de pago Marroquin 2", direccion: "- CR 26G9 73 39" },
      { nombre: "Punto de pago Marroquin 1", direccion: "- CR 25P5 87 67" },
    ],

    personalAdicional: [
      {
        nombre: "Maira Hernandez",
        cargo: "Retencion, caja",
        pbx: "1105",
        telefono: "3128655642",
      },
    ],
  },
  {
    nombre: "Oficina Chorros",
    direccion: "CL 1 BIS OESTE 73D 85B - Mario Correa ",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Sharon Fuentes",
    pbx: "1110",
    telefono: "3182838808",
    barrios: [
      "Mario Correa",
      "Chorros",
      "Louders",
      "Prados del Sur",
      "La marranera",
      "Golositos",
      "Alto Napoles",
    ],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Sala de internet Marlin", direccion: "- CL 3C OESTE 90 15" },
      { nombre: "Miscelanea Raquel", direccion: "- CR 94A 1 60" },
    ],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Montebello",
    direccion: "CL 12 OESTE 42 12 - Centro Montebello ",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Jhoiner Arturo Barbosa Otalvaro",
    pbx: "1211",
    telefono: "3147773428",
    barrios: ["Montebello"],
    planes: [
      { nombre: "200 megas", precio: "$50.000" },
      { nombre: "350 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$80.000" },
    ],
    puntosRecaudo: [
      { nombre: "Tienda Kary", direccion: "- AV 47 OESTE 9A 112" },
      { nombre: "Tienda Luz Mery", direccion: "- CL 4 OESTE 43A 07" },
      { nombre: "Parasol Rojo", direccion: "- CL 12 OESTE 36 42" },
    ],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Siloe",
    direccion: "CL 1 OESTE 52 370 - Belisario Siloe ",
    ciudad: "Cali",
    departamento: "Valle del cauca",
    administrador: "Mayely Mosquera",
    pbx: "1111",
    telefono: "3188073456",
    barrios: ["Siloe"],
    planes: [
      { nombre: "20 megas", precio: "$60.000" },
      { nombre: "TV", precio: "$30.000" },
    ],
    puntosRecaudo: [
      {
        nombre: "Punto de recaudo Maitte Silva",
        direccion: "- CL 10 OESTE 49C 40",
      },
      {
        nombre: "Punto de recaudo Eimmy muñoz",
        direccion: "- CL 10 OESTE 50 53",
      },
      {
        nombre: "Punto de recaudo Monica Valencia",
        direccion: "- CL 14 OESTE 48 68",
      },
      {
        nombre: "Punto de recaudo Geidy Martinez",
        direccion: "- CL 13 531 42",
      },
    ],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Cerrito",
    direccion: "CL 6 6 50 - La Estrella ",
    ciudad: "Cerrito",
    departamento: "Valle del cauca",
    administrador: "Angelica Hurtado Silva",
    pbx: "1211",
    telefono: "3215931208",
    barrios: [
      "Nuevo Municipio(HFC)",
      "Nuevo Amanecer(HFC)",
      "El Saman(HFC)",
      "Villa Lina(HFC)",
      "Porvenir(HFC)",
      "La Paz (HFC)",
      "Coincer(HFC)",
      "Los Samanes(HFC)",
      "Villa Del Carmen(HFC)",
      "Asovicons(HFC)",
      "Rincones(HFC)",
      "La Esperanza(HFC)",
      "Pueblito Valluno(HFC)",
      "Prado Valle(HFC)",
      "Las Orquideas(HFC)",
      "Buenos Aires(GPON)",
      "Cincuentenario(GPON)",
      "Lares Del Paraiso(GPON)",
      "La Estrella(GPON)",
      "San Rafael(GPON)",
      "Chapinero(SOLO TV)",
      "Santa Barbara(SOLO TV)",
    ],
    planes: [
      { nombre: "TV + NET 50 MB", precio: "" },
      { nombre: "TV + NET 100 MB", precio: "" },
      { nombre: "TV + NET 200 MB", precio: "" },
      { nombre: "TV + NET 300 MB", precio: "" },
      { nombre: "TV + NET 20 MB HFC", precio: "" },
    ],
    puntosRecaudo: [
      {
        nombre: "Punto de recuado Jose Rodrigo Garcia",
        direccion: "- CL 9 13 65",
      },
    ],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Andalucia",
    direccion: "CL 12 5 49 - Centro",
    ciudad: "Andalucia",
    departamento: "Valle del cauca",
    administrador: "Maria José Arias Hernández",
    pbx: "1215",
    telefono: "3154106483",
    barrios: [
      "Alianza",
      "Altamira",
      "Centenario",
      "Centro",
      "Colinas",
      "Estacion",
      "Floresta 1",
      "Floresta 2",
      "Paraiso",
      "Reubicacion",
      "Sol y luna",
      "Retorno",
    ],
    planes: [
      { nombre: "20 megas", precio: "$40.000" },
      { nombre: "TV", precio: "$30.000" },
    ],
    puntosRecaudo: [{ nombre: "NO", direccion: "" }],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Tulua",
    direccion: "TVSAL 12 25B 56 - Bolivar ",
    ciudad: "Tulua",
    departamento: "Valle del cauca",
    administrador: "Maria del Carmen Morales ",
    pbx: "1207",
    telefono: "3169621818 - 3169043288",
    barrios: [
      "Saman (TV HFC)",
      "Portales del rio (TV HFC)",
      "Alameda 1 (TV HFC)",
      "Alameda 2 (TV HFC)",
      "Palmar (TV HFC)",
      "Internacional (TV HFC)",
      "Villa colombia (TV HFC)",
      "Municipal (TV HFC)",
      "Bosquesito (TV HFC)",
      "Jorge eliecer gaitan (TV HFC)",
      "Chimangos (TV HFC)",
      "Bello horizonte (TV HFC)",
      "Refugio (TV HFC)",
      "Diablos rojos (TV HFC)",
      "Flor de la campana (TV HFC)",
      "La esperanza (TV HFC)",
      "Las delicias (TV HFC)",
      "La ceiba (TV HFC)",
      "Rojas (TV HFC)",
      "Ruben cruz (TV HFC)",
      "Los olmos (TV HFC)",
      "Playas (TV HFC)",
      "Graciela (TV HFC)",
      "El jardin (TV HFC)",
      "Maracaibo (TV HFC)",
      "Siete de agosto (TV HFC)",
      "Sintra sancarlos (TV HFC)",
      "Portales de rio paila (TV HFC)",
      "Santa ines (TV HFC)",
      "Guayacanes (TV HFC)",
      "San luis (TV HFC)",
      "Estambul (TV HFC)",
      "Farfan (TV HFC)",
      "Nuevo farfan (TV HFC)",
      "Asoagrin (TV HFC)",
      "Limonar (TV HFC)",
      "San arino (TV HFC)",
      "Laures 1 (TV HFC)",
      "Laureles 2 (TV HFC)",
      "Villa del lago (TV HFC)",
      "La nieves (TV HFC)",
      "Veraneras (TV HFC)",
      "Popular (TV HFC)",
      "Villanueva (TV HFC)",
      "Santarita et2 (TV HFC)",
      "Moralito (TV HFC)",
      "Bosque de maracaibo (NET + TV HFC)",
      "Tercer milenio (NET + TV HFC)",
      "El bosque (NET + TV HFC)",
      "La cruz (NET + TV HFC)",
      "Av cali (NET + TV HFC)",
      "Salecianos (NET + TV HFC)",
      "La bastilla (NET + TV HFC)",
    ],
    planes: [
      { nombre: "20 megas", precio: "$40.000" },
      { nombre: "TV", precio: "$30.000" },
    ],
    puntosRecaudo: [
      { nombre: "Punto de pago Diego Cell", direccion: "CL 13 49 20" },
      { nombre: "Carlos Evandro Vanegas", direccion: "CL 12 A 28B 72" },
    ],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Tarqui",
    direccion: "CL 3 5 61 LOCAL 2 - Antonio Ricaute",
    ciudad: "Tarqui",
    departamento: "Valle del cauca",
    administrador: "Claudia Patricia Jaramillo Montoya",
    pbx: "1104",
    telefono: "3204564217",
    barrios: [
      "Villas del canadá (TV HFC)",
      "Manuel de jesús (TV HFC)",
      "Villa magdalena (TV HFC)",
      "San antonio (TV HFC)",
      "Villa del rio (TV HFC)",
      "Antonio ricaute (TV HFC)",
      "Centro (TV HFC)",
      "La veguita (TV HFC)",
      "Vereda san joaquín (TV HFC)",
      "La loma (TV HFC)",
      "La bodega (TV HFC)",
      "Villa aurora (TV HFC)",
      "Ciudadela otoniel rojas correa (TV HFC)",
      "Hato nuevo (TV HFC)",
      "Minuto de dios (TV HFC)",
      "Portal del sur (TV HFC)",
      "Portal del sur 2da etapa (TV HFC)",
      "El estadio (TV HFC)",
      "El jardín (TV HFC)",
      "Circunvalar (TV HFC)",
      "Las brisas (TV HFC)",
      "Las brisas 2da etapa (TV HFC)",
      "Llano del hato o san josé obrero (TV HFC)",
      "Circunvalar (TV HFC)",
      "San joaquin (TV HFC)",
      "Villas del canada (NET + TV HFC)",
      "Antonio ricaute (NET + TV HFC)",
      "Las brisas (NET + TV HFC)",
      "Centro (NET + TV HFC)",
      "La loma (NET + TV HFC)",
      "Villa aurora (NET + TV HFC)",
      "Hato nuevo (NET + TV HFC)",
      "Minuto de dios (NET + TV HFC)",
      "Portal del sur 2 etapa (NET + TV HFC)",
    ],
    planes: [
      { nombre: "20 megas", precio: "$50.000" },
      { nombre: "TV", precio: "$30.000" },
    ],
    puntosRecaudo: [{ nombre: "NO", direccion: "" }],

    personalAdicional: [
      {
        nombre: "NO",
        cargo: "NO",
        pbx: "",
        telefono: "",
      },
    ],
  },
  {
    nombre: "Oficina Florida",
    direccion: "CL 9 16 74 - Florida",
    ciudad: "Florida",
    departamento: "Valle del cauca",
    administrador: "Maylhen Melo",
    pbx: "1107",
    telefono: "3188139581-3207591526",
    barrios: ["Florida"],
    planes: [
      { nombre: "100 megas", precio: "$50.000" },
      { nombre: "300 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$95.000" },
    ],
    puntosRecaudo: [
      {
        nombre: "Tu amigo comunicaciones",
        direccion: "- CL 10 12 33 San antonio",
      },
      { nombre: "Aqui es eliza irleym", direccion: "- CR 13 7 37 La cabaña" },
      { nombre: "Miscelanea@tramites", direccion: "- CL 9 21 44 La esperanza" },
      { nombre: "Multiservicios", direccion: "- CR 14 5 04 La cabaña" },
      { nombre: "Variedades pao", direccion: "- CR 20 13 34 San jorge" },
      { nombre: "Autoservicio ja en la 10", direccion: "- CL 10 3 64" },
      { nombre: "Tienda llanito", direccion: "- Llanito" },
    ],

    personalAdicional: [
      {
        nombre: "Lizeth Johanna",
        cargo: "Retencion",
        pbx: "1118",
        telefono: "NO",
      },
      {
        nombre: "Cintya Cuaran",
        cargo: "Caja",
        pbx: "NO",
        telefono: "NO",
      },
      {
        nombre: "Sebastian Prada",
        cargo: "Cartera",
        pbx: "1114",
        telefono: "NO",
      },
    ],
  },
  {
    nombre: "Oficina Pradera",
    direccion: "CL 6 11 25 - Centro",
    ciudad: "Pradera",
    departamento: "Valle del cauca",
    administrador: "Mayra Alejandra Rivera",
    pbx: "1220",
    telefono: "3218117199",
    barrios: ["Pradera"],
    planes: [
      { nombre: "100 megas", precio: "$50.000" },
      { nombre: "300 megas", precio: "$70.000" },
      { nombre: "600 megas", precio: "$95.000" },
    ],
    puntosRecaudo: [
      {
        nombre: "Papeleria Panda",
        direccion: "- MZ C CS 1 La Lorena",
      },
      { nombre: "Aqui es eliza irleym", direccion: "- CL 10 8 48 San Roque " },
      {
        nombre: "Interrapidisimo - Multiservicios",
        direccion: "- CL 8 13 53 Ant. Ricaute",
      },
    ],

    personalAdicional: [
      {
        nombre: "Jackeline Rondon",
        cargo: "Caja",
        pbx: "1108",
        telefono: "NO",
      },
    ],
  },
];

const oficinasFiltradas = computed(() => {
  return oficinasData.filter((oficina) => {
    const matchTexto =
      !filtroTexto.value ||
      oficina.nombre.toLowerCase().includes(filtroTexto.value.toLowerCase()) ||
      oficina.direccion
        .toLowerCase()
        .includes(filtroTexto.value.toLowerCase()) ||
      oficina.administrador
        .toLowerCase()
        .includes(filtroTexto.value.toLowerCase()) ||
      oficina.pbx.toLowerCase().includes(filtroTexto.value.toLowerCase());

    const matchCiudad =
      filtroCiudad.value === "Todos" || oficina.ciudad === filtroCiudad.value;

    return matchTexto && matchCiudad;
  });
});

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
  padding: 30px 40px;
  background: linear-gradient(to bottom, #f8f9fa 0%, #e9ecef 100%);
  min-height: calc(100vh - 90px);
}

.oficinas-header {
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

.button-nueva-oficina {
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

.button-nueva-oficina:hover {
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
  align-items: flex-end; /* Alinea los elementos al final */
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
  height: 50px; /* Altura fija */
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

.filter-box-select {
  display: flex;
  flex-direction: column;
  gap: 6px;
  flex: 1;
  min-width: 200px;
}

.filter-label {
  font-size: 13px;
  font-weight: 600;
  color: #64748b;
}

.filter-select {
  padding: 12px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  font-size: 15px;
  color: #1e293b;
  background: white;
  cursor: pointer;
  transition: all 0.3s ease;
  outline: none;
  height: 50px; /* Misma altura que el input */
}   

.filter-select:focus {
  border-color: #2a5298;
  box-shadow: 0 0 0 4px rgba(42, 82, 152, 0.08);
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

.oficinas-table {
  width: 100%;
  border-collapse: collapse;
}

.oficinas-table thead {
  background: #f8fafc;
}

.oficinas-table th {
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

.oficinas-table td {
  padding: 18px 20px;
  font-size: 15px;
  color: #334155;
}

.oficina-cell {
  display: flex;
  align-items: center;
  gap: 12px;
}

.oficina-icon {
  font-size: 20px;
}

.oficina-nombre {
  font-weight: 600;
  color: #1e3c72;
}

.extension {
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
  .layout-oficinas {
    padding: 20px;
  }

  .oficinas-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .page-title {
    font-size: 24px;
  }

  .button-nueva-oficina {
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

  .oficinas-table {
    min-width: 800px;
  }
}
</style>
