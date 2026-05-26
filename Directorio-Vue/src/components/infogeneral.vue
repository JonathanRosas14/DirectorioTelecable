<template>
  <div class="infogeneral">
    <div class="info-header">
      <h1 class="page-title">🏦 Información General</h1>
      <p class="page-subtitle">Cuentas bancarias, convenios y directorio corporativo</p>
    </div>

    <!-- Sección Cuentas Bancarias -->
    <div class="section-title">
      <span class="section-icon">💳</span>
      <h2>Cuentas Bancarias y Convenios</h2>
    </div>

    <div class="cuentas-grid">
      <!-- Cuenta Telecable -->
      <div class="cuenta-card">
        <div class="cuenta-header telecable">
          <div class="cuenta-icon">🏢</div>
          <div class="cuenta-info">
            <h2 class="cuenta-nombre">TELECABLE</h2>
            <div class="cuenta-detalles">
              <div class="detalle-item">
                <span class="detalle-label">Cuenta:</span>
                <span class="detalle-valor">061-000047-08</span>
              </div>
              <div class="detalle-item">
                <span class="detalle-label">Convenio:</span>
                <span class="detalle-valor badge-convenio">4451</span>
              </div>
            </div>
          </div>
        </div>
        <div class="cuenta-body">
          <h3 class="oficinas-titulo">Oficinas asociadas:</h3>
          <div class="oficinas-grid">
            <span class="oficina-badge">Comuneros</span>
            <span class="oficina-badge">Marroquín</span>
            <span class="oficina-badge">Río Cauca</span>
            <span class="oficina-badge">Antonio Nariño</span>
          </div>
        </div>
      </div>

      <!-- Cuenta Home TV -->
      <div class="cuenta-card">
        <div class="cuenta-header hometv">
          <div class="cuenta-icon">📺</div>
          <div class="cuenta-info">
            <h2 class="cuenta-nombre">HOME TV</h2>
            <div class="cuenta-detalles">
              <div class="detalle-item">
                <span class="detalle-label">Cuenta:</span>
                <span class="detalle-valor">710-286988-50</span>
              </div>
              <div class="detalle-item">
                <span class="detalle-label">Convenio:</span>
                <span class="detalle-valor badge-convenio">93323</span>
              </div>
            </div>
          </div>
        </div>
        <div class="cuenta-body">
          <h3 class="oficinas-titulo">Oficinas asociadas:</h3>
          <div class="oficinas-grid">
            <span class="oficina-badge">Chorros</span>
            <span class="oficina-badge">Siloe</span>
            <span class="oficina-badge">Poblado</span>
            <span class="oficina-badge">Córdoba</span>
            <span class="oficina-badge">Villanueva</span>
            <span class="oficina-badge">Montebello</span>
            <span class="oficina-badge">Tarqui</span>
          </div>
        </div>
      </div>

      <!-- Cuenta Inversiones CYC -->
      <div class="cuenta-card">
        <div class="cuenta-header inversiones">
          <div class="cuenta-icon">💼</div>
          <div class="cuenta-info">
            <h2 class="cuenta-nombre">INVERSIONES CYC</h2>
            <div class="cuenta-detalles">
              <div class="detalle-item">
                <span class="detalle-label">Cuenta:</span>
                <span class="detalle-valor">836-000030-69</span>
              </div>
              <div class="detalle-item">
                <span class="detalle-label">Convenio:</span>
                <span class="detalle-valor badge-convenio">7144</span>
              </div>
            </div>
          </div>
        </div>
        <div class="cuenta-body">
          <h3 class="oficinas-titulo">Oficinas asociadas:</h3>
          <div class="oficinas-grid">
            <span class="oficina-badge">Tuluá</span>
            <span class="oficina-badge">Cerrito</span>
            <span class="oficina-badge">Florida</span>
            <span class="oficina-badge">Pradera</span>
            <span class="oficina-badge">Andalucía</span>
            <span class="oficina-badge">Carmelo</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Sección Correos Corporativos -->
    <div class="section-title">
      <span class="section-icon">📧</span>
      <h2>Correos Corporativos</h2>
    </div>

    <div class="correos-container">

      <div class="table-card">
        <div class="table-container">
          <table class="correos-table">
            <thead>
              <tr>
                <th>Nombre</th>
                <th>Cargo</th>
                <th>Correo</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(persona, index) in correosData" :key="index" class="table-row">
                <td class="persona-nombre">{{ persona.nombre }}</td>
                <td class="persona-cargo">{{ persona.cargo }}</td>
                <td class="persona-correo">
                  <a v-if="persona.correo" :href="'mailto:' + persona.correo" class="correo-link">
                    {{ persona.correo }}
                  </a>
                  <span v-else class="sin-correo">-</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const correosData = [
  {
    nombre: "ANYELA VIERA CASTILLO",
    cargo: "REPRESENTANTE LEGAL",
    correo: "representantelegal@telecable.com.co"
  },
  {
    nombre: "ELIZABETH ROMERO DELGADO",
    cargo: "CONTADORA/REVISORIA FISCAL",
    correo: "contabilidad@telecable.com.co"
  },
  {
    nombre: "NATALIA MELISSA VALENCIA CASTAÑO",
    cargo: "CONTADORA",
    correo: "contabilidad@telecable.com.co"
  },
  {
    nombre: "ELIANA ERAZO IPUZ",
    cargo: "AUXILIAR CONTABLE",
    correo: "auxiliar.contable@telecable.com.co"
  },
  {
    nombre: "LIDA KARINA MARIÑO",
    cargo: "JEFE CARTERA",
    correo: "cartera@telecable.com.co"
  },
  {
    nombre: "JHOELYN VALERIA BARONA CASTRO",
    cargo: "AUXILIAR CARTERA",
    correo: "cartera@telecable.com.co"
  },
  {
    nombre: "LUNA FERNADA MORA NORIEGA",
    cargo: "AUXILIAR CARTERA",
    correo: "cartera@telecable.com.co"
  },
  {
    nombre: "NASLY JOHANNA HERNANDEZ SALCEDO",
    cargo: "SUBGERENTE",
    correo: "subgerencia@telecable.com.co"
  },
  {
    nombre: "VALENTINA TAMAYO ORTEGA",
    cargo: "ASISTENTE SUBGERENCIA",
    correo: "subgerencia@telecable.com.co"
  },
  {
    nombre: "KATHERIN MUÑOZ GOMEZ",
    cargo: "RECURSOS HUMANOS",
    correo: "recursoshumanos@telecable.com.co"
  },
  {
    nombre: "IVET YIZETH LEDEZMA MORENO",
    cargo: "AUXILIAR RECURSOS HUMANOS",
    correo: "recursoshumanos@telecable.com.co"
  },
  {
    nombre: "CLAUDIA PATRICIA VARGAS CASTAÑEDA",
    cargo: "ADMINISTRADORA OFICINA COMUNEROS",
    correo: "oficinacomuneros@telecable.com.co"
  },
  {
    nombre: "ANA YIBEL CRUZ",
    cargo: "AUXILIAR/CAJERA OFICINA COMUNEROS",
    correo: "oficinacomuneros@telecable.com.co"
  },
  {
    nombre: "LEYDI JOHANA OSPINA ORDOÑEZ",
    cargo: "ADMINISTRADORA OFICINA MARROQUIN",
    correo: "oficinamarroquin@telecable.com.co"
  },
  {
    nombre: "MAYRA ALEJANDRA HERNANDEZ CASTAÑEDA",
    cargo: "AUXILIAR/CAJERA OFICINA MARROQUIN",
    correo: "oficinamarroquin@telecable.com.co"
  },
  {
    nombre: "YULIANA ALEJANDRA GIRALDO PEREZ",
    cargo: "ADMINISTRADORA OFICINA RIO CAUCA",
    correo: "oficinariocauca@telecable.com.co"
  },
  {
    nombre: "JHAN PAUL SARRIA BERNAL",
    cargo: "AUXILIAR/CAJERO OFICINA RIO CAUCA",
    correo: "oficinariocauca@telecable.com.co"
  },
  {
    nombre: "DAVID SOLANO DIAZ",
    cargo: "DIBUJANTE PROYECTOS",
    correo: "proyectos.infraestructura@telecable.com.co"
  }
]
</script>

<style scoped>
.infogeneral {
  padding: 30px 40px;
  background: linear-gradient(to bottom, #f8f9fa 0%, #e9ecef 100%);
  min-height: calc(100vh - 90px);
}

.info-header {
  margin-bottom: 40px;
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

.section-title {
  display: flex;
  align-items: center;
  gap: 12px;
  margin: 40px 0 24px 0;
  padding-bottom: 12px;
  border-bottom: 3px solid #e2e8f0;
}

.section-icon {
  font-size: 28px;
}

.section-title h2 {
  font-size: 24px;
  font-weight: 700;
  color: #1e3c72;
  margin: 0;
}

.cuentas-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(450px, 1fr));
  gap: 30px;
  margin-bottom: 50px;
}

.cuenta-card {
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
}

.cuenta-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.12);
}

.cuenta-header {
  padding: 30px;
  display: flex;
  gap: 20px;
  align-items: flex-start;
  position: relative;
  overflow: hidden;
}

.cuenta-header::before {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  opacity: 0.1;
  transform: translate(30%, -30%);
  background: white;
}

.cuenta-header.telecable {
  background: linear-gradient(135deg, #5a7ca0 0%, #3d5a80 100%);
}

.cuenta-header.hometv {
  background: linear-gradient(135deg, #6b9080 0%, #4f7a67 100%);
}

.cuenta-header.inversiones {
  background: linear-gradient(135deg, #8b7fa6 0%, #6d5a8c 100%);
}

.cuenta-icon {
  font-size: 48px;
  width: 80px;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  backdrop-filter: blur(10px);
  flex-shrink: 0;
}

.cuenta-info {
  flex: 1;
  position: relative;
  z-index: 1;
}

.cuenta-nombre {
  font-size: 24px;
  font-weight: 700;
  color: white;
  margin: 0 0 16px 0;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.cuenta-detalles {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.detalle-item {
  display: flex;
  align-items: center;
  gap: 10px;
}

.detalle-label {
  font-size: 13px;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.8);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.detalle-valor {
  font-size: 16px;
  font-weight: 700;
  color: white;
  font-family: 'Courier New', monospace;
}

.badge-convenio {
  padding: 6px 14px;
  background: rgba(255, 255, 255, 0.25);
  border-radius: 20px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.cuenta-body {
  padding: 30px;
  background: linear-gradient(to bottom, #f8fafc 0%, white 100%);
}

.oficinas-titulo {
  font-size: 15px;
  font-weight: 700;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin: 0 0 16px 0;
}

.oficinas-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.oficina-badge {
  padding: 10px 18px;
  background: white;
  border: 2px solid #e2e8f0;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  color: #3d5a80;
  transition: all 0.3s ease;
}

.oficina-badge:hover {
  border-color: #5a7ca0;
  background: linear-gradient(135deg, rgba(90, 124, 160, 0.1) 0%, rgba(61, 90, 128, 0.1) 100%);
  transform: translateY(-2px);
}

.correos-container {
  margin-bottom: 30px;
}


.nit-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nit-label {
  font-size: 20px;
  font-weight: 700;
  color: white;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.nit-value {
  font-size: 18px;
  font-weight: 700;
  color: white;
  font-family: 'Courier New', monospace;
  background: rgba(255, 255, 255, 0.2);
  padding: 8px 16px;
  border-radius: 8px;
}

.table-card {
  background: white;
  border-radius: 0 0 16px 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  overflow: hidden;
}

.table-container {
  overflow-x: auto;
}

.correos-table {
  width: 100%;
  border-collapse: collapse;
}

.correos-table thead {
  background: #f8fafc;
}

.correos-table th {
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

.correos-table td {
  padding: 16px 20px;
  font-size: 14px;
  color: #334155;
}

.persona-nombre {
  font-weight: 600;
  color: #1e3c72;
}

.persona-cargo {
  color: #64748b;
  font-size: 13px;
}

.correo-link {
  color: #5a7ca0;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s ease;
}

.correo-link:hover {
  color: #3d5a80;
  text-decoration: underline;
}

.sin-correo {
  color: #cbd5e1;
}

@media (max-width: 1024px) {
  .infogeneral { padding: 32px 28px; }
  .page-title { font-size: 36px; }
  .section-title h2 { font-size: 22px; }
}

@media (max-width: 768px) {
  .infogeneral { padding: 24px 16px; }
  .page-title { font-size: 30px; }
  .page-desc { font-size: 16px; }
  .section-title h2 { font-size: 20px; }

  .cuentas-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .cuenta-header {
    flex-direction: column;
    padding: 24px;
  }

  .cuenta-icon {
    width: 60px;
    height: 60px;
    font-size: 36px;
  }

  .cuenta-nombre {
    font-size: 20px;
  }

  .nit-info {
    flex-direction: column;
    gap: 12px;
    text-align: center;
  }

  .table-container {
    overflow-x: auto;
  }

  .correos-table {
    min-width: 700px;
  }
}

@media (max-width: 480px) {
  .infogeneral { padding: 20px 12px; }
  .page-title { font-size: 26px; }
  .page-desc { font-size: 15px; }
  .section-title h2 { font-size: 18px; }
  .cuenta-header { padding: 20px; }
  .cuenta-body { padding: 16px 20px; }
  .ofi-tag { font-size: 12px; padding: 5px 10px; }
}
</style>