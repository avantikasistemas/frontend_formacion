<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>
        <div class="container">
            <div class="form-container">
                <h2>Consultar</h2>
                <form class="form-flex">
                    <div class="form-group">
                        <label>Código de la Formación:</label>
                        <input type="text" class="input-field" v-model="codigo">
                    </div>
                    <div class="form-group">
                        <label>Tema:</label>
                        <input type="text" class="input-field" v-model="tema">
                    </div>
                    <div class="form-group">
                        <label>Macroproceso:</label>
                        <select class="input-field" v-model="macroproceso">
                            <option :value="null">Seleccione...</option>
                            <option v-for="macro in list_macroprocesos" :value="macro.id">{{ macro.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Usuario:</label>
                        <input 
                            type="text" 
                            class="input-field" 
                            v-model="usuarioBusqueda" 
                            @focus="mostrarLista = true" 
                            @blur="ocultarLista"
                            placeholder="Escriba el usuario..."
                        >
                        <ul v-if="mostrarLista && usuarios_list.length" class="dropdown-list">
                            <li v-for="usu in usuarios_list" :key="usu.cedula" @mousedown="seleccionarUsuario(usu)">
                                {{ usu.nombre }}
                            </li>
                        </ul>
                    </div>
                    <div class="form-group">
                        <label>Nivel de Formación:</label>
                        <select class="input-field" v-model="nivel_formacion">
                            <option :value="null">Seleccione...</option>
                            <option v-for="niv_form in list_tipo_nivel_formacion" :value="niv_form.id">{{ niv_form.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Tipo de Actividad:</label>
                        <select class="input-field" v-model="tipo_actividad">
                            <option :value="null">Seleccione...</option>
                            <option v-for="tip_act in list_tipo_actividad" :value="tip_act.id">{{ tip_act.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Modalidad:</label>
                        <select class="input-field" v-model="modalidad">
                            <option :value="null">Seleccione...</option>
                            <option v-for="tip_mod in list_tipo_modalidad" :value="tip_mod.id">{{ tip_mod.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Estado Formación:</label>
                        <select class="input-field" v-model="estado_formacion">
                            <option :value="null">Seleccione...</option>
                            <option v-for="est_for in list_estados_formacion" :value="est_for.id">{{ est_for.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Fecha Desde:</label>
                        <input type="date" class="input-field" v-model="fecha_desde">
                    </div>
                    <div class="form-group">
                        <label>Fecha Hasta:</label>
                        <input type="date" class="input-field" v-model="fecha_hasta" :min="fecha_desde">
                    </div>
                </form>
                <div class="buttons">
                    <button class="consultar" @click="consultarDatos">Consultar</button>
                    <button class="limpiar" @click="limpiar">Limpiar</button>
                </div>
            </div>
            <div class="form-container">
                <div class="table-container">
                    <h3 class="h3-title">FORMACIONES</h3>
                    <table>
                        <thead>
                            <tr>
                                <th># ID</th>
                                <th>CÓDIGO</th>
                                <th>TEMA</th>
                                <th>NIVEL DE FORMACIÓN</th>
                                <th>TIPO DE ACTIVIDAD</th>
                                <th>MODALIDAD</th>
                                <th>ESTADO</th>
                                <th>PERSONAL</th>
                                <th>MACROPROCESO</th>
                                <th>FECHA INICIO</th>
                                <th>FECHA FIN</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-if="lista_registros.length === 0">
                                <td colspan="9" class="no-registros">No hay registros disponibles</td>
                            </tr>
                            <tr v-else v-for="(reg, index) in lista_registros" :key="index" @mouseover="selectRow(index)" :class="{ 'selected-row': index === selectedRowId }">
                                <td>{{ reg.id }}</td>
                                <td>{{ reg.codigo }}</td>
                                <td>{{ reg.tema }}</td>
                                <td>{{ reg.nivel_formacion }}</td>
                                <td>{{ reg.tipo_actividad }}</td>
                                <td>{{ reg.modalidad }}</td>
                                <td>{{ reg.estado_formacion }}</td>
                                <td>{{ reg.nombre }}</td>
                                <td>{{ reg.macroproceso }}</td>
                                <td>{{ reg.fecha_inicio }}</td>
                                <td>{{ reg.fecha_fin }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <div class="pagination" v-if="total_registros > 15">
                <label for="records-per-page">Registros por página:</label>
                <select 
                    id="records-per-page" 
                    v-model="limit" 
                    @change="changePage(1)"
                >
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="50">50</option>
                </select>
                <button 
                    :disabled="position <= 1" 
                    @click="changePage(1)"
                >
                    Primera
                </button>
                
                <button 
                    :disabled="position <= 1" 
                    @click="changePage(position - 1)"
                >
                    Anterior
                </button>
                
                <span>Página {{ position }} de {{ total_paginas }}</span>
                
                <button 
                    :disabled="position >= total_paginas" 
                    @click="changePage(position + 1)"
                >
                    Siguiente
                </button>
                
                <button 
                    :disabled="position >= total_paginas" 
                    @click="changePage(total_paginas)"
                >
                    Última
                </button>
                </div>
            </div>
        </div>

        <!-- Modal de éxito -->
        <div class="modal fade" id="exitoModal" tabindex="-1" aria-labelledby="exitoModalLabel" aria-hidden="true" data-bs-backdrop="static" ref="exitoModal">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exitoModalLabel">Modal Formación</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        {{ msg }}
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal de error -->
        <div class="modal fade" id="errorModal" tabindex="-1" aria-labelledby="errorModalLabel" aria-hidden="true" data-bs-backdrop="static" ref="errorModal">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="errorModalLabel">Error</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        {{ errorMsg }}
                    </div>
                    <div class="modal-footer" v-if="token_status===401">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="logout">Cerrar</button>
                    </div>
                    <div class="modal-footer" v-else-if="token_status===403">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="redirigir_home">Cerrar</button>
                    </div>
                    <div class="modal-footer" v-else>
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                    </div>
                </div>
            </div>
        </div>
    </LayoutView>
</template>
  
<script setup>
import apiUrl from "../../config.js";
import LayoutView from '../views/Layouts/LayoutView.vue';
import { ref, onMounted, watch } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import { Modal } from 'bootstrap';

const token = ref("");
const nombre = ref("");
const cedula = ref("");

const list_tipo_nivel_formacion = ref([]);
const list_tipo_actividad = ref([]);
const list_macroprocesos = ref([]);
const list_tipo_modalidad = ref([]);
const list_estados_formacion = ref([]);

const codigo = ref('');
const nivel_formacion = ref(null);
const tipo_actividad = ref(null);
const tema = ref("");
const macroproceso = ref(null);
const modalidad = ref(null);
const usuarios_list = ref([]);
const estado_formacion = ref(null);
const fecha_desde = ref(null);
const fecha_hasta = ref(null);
const fechaDesdeFormateada = ref(null);
const fechaHastaFormateada = ref(null);

const mostrarLista = ref(false);
const usuarioBusqueda = ref("");
const usuarioId = ref("");
const selectedRowId = ref("");

const lista_registros = ref([]);
const total_paginas = ref(0);
const total_registros = ref(0);
const limit = ref(15);
const position = ref(1);

const modalInstance = ref(null);
const modalErrorInstance = ref(null);

const token_status = ref(0);

const msg = ref("");
const errorMsg = ref("");

const router = useRouter();


// ✅ Función para cargar los datos a la tabla
const consultarDatos = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }

        if (fecha_desde.value){
            fechaDesdeFormateada.value = fecha_desde.value.replace(/-/g, ""); // Reemplaza los guiones por nada
        }
        if (fecha_hasta.value){
            fechaHastaFormateada.value = fecha_hasta.value.replace(/-/g, ""); // Reemplaza los guiones por nada
        }

        const response = await axios.post(
            `${apiUrl}/consultar_datos`, 
            {
                codigo: codigo.value,
                tema: tema.value,
                macroproceso: macroproceso.value,
                usuario: usuarioId.value,
                nivel_formacion: nivel_formacion.value,
                tipo_actividad: tipo_actividad.value,
                modalidad: modalidad.value,
                estado_formacion: estado_formacion.value,
                fecha_desde: fechaDesdeFormateada.value,
                fecha_hasta: fechaHastaFormateada.value,
                limit: parseInt(limit.value),
                position: parseInt(position.value),
            },
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            msg.value = response.data.message;
            lista_registros.value = response.data.data.registros;
            total_paginas.value = response.data.data.total_pag;
            total_registros.value = response.data.data.total_registros;
        }

    } catch (error) {
        console.error('Error al cargar los datos:', error);
        modalErrorInstance.value.show();
        errorMsg.value = error.response.data.message;
        if (error.response.status === 401) {
          token_status.value = error.response.status
          errorMsg.value = error.response.data.detail;
        } else if (error.response.status === 403) {
            token_status.value = error.response.status
            errorMsg.value = error.response.data.detail;
        }
    }
};

// ✅ Función para cargar los select generales del formulario
const cargarDatos = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/get_parametros`, {},
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            msg.value = response.data.message;
            list_tipo_nivel_formacion.value = response.data.data.nivel_formacion;
            list_tipo_actividad.value = response.data.data.tipo_actividad;
            list_macroprocesos.value = response.data.data.macroprocesos;
            list_tipo_modalidad.value = response.data.data.tipo_modalidad;
            list_estados_formacion.value = response.data.data.formacion_estados;
        }

    } catch (error) {
        console.error('Error al cargar los datos:', error);
        modalErrorInstance.value.show();
        errorMsg.value = error.response.data.message;
        if (error.response.status === 401) {
          token_status.value = error.response.status
          errorMsg.value = error.response.data.detail;
        } else if (error.response.status === 403) {
            token_status.value = error.response.status
            errorMsg.value = error.response.data.detail;
        }
    }
};

// Función para manejar el cierre de sesión
function logout() {
  localStorage.clear();
  router.push('/'); // Redirigir al login
};

// ✅ Función que redirige al a página principal
function redirigir_home() {
  router.push('/registro'); // Redirigir al dashboard
};

// ✅ Función para limpiar campos
const limpiar = () => {
    codigo.value = '';
    tema.value = '';
    usuarioBusqueda.value = '';
    nivel_formacion.value = null;
    tipo_actividad.value = null;
    estado_formacion.value = null;
    macroproceso.value = null;
    modalidad.value = null;
    fecha_desde.value = null;
    fecha_hasta.value = null;
    lista_registros.value = [];
    total_registros.value = 0;
    position.value = 1;
    limit.value = 15;
};

// ✅ Función para seleccionar el registro a editar
const selectRow = (index) => {
    selectedRowId.value = index;
};

// ✅ Función que selecciona los datos del proveedor elegido en el input de proveedor
const seleccionarUsuario = (usu) => {
    usuarioBusqueda.value = usu.nombre;
    usuarioId.value = usu.cedula;
    mostrarLista.value = false;
};

// ✅ Función para ocultar la lista
const ocultarLista = () => {
    setTimeout(() => {
        mostrarLista.value = false;
    }, 200);
};

// ✅ Watcher que esta pendiente si hay un cambio en el campo de busqueda
watch(usuarioBusqueda, async (nuevoValor) => {
    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }
    if (nuevoValor.length >= 2) { // Iniciar búsqueda después de 2 caracteres
        try {
            const response = await axios.post(`${apiUrl}/obtener_todo_personal_activo`, 
                {
                    valor: nuevoValor
                },
                {
                    headers: {
                        Accept: "application/json",
                        Authorization: `Bearer ${token.value}`
                    }
                }
            );
            usuarios_list.value = response.data.data;
        } catch (error) {
            console.error("Error en la búsqueda:", error);
        }
    } else {
        usuarios_list.value = [];
    }
});

// ✅ Función para cambiar pagina del paginador
const changePage = async (newPosition) => {
  position.value = newPosition;
  await consultarDatos(); // Vuelve a cargar los datos con el nuevo límite y posición
};

// ✅ Función mounted que carga información ANTES de que la página renderice
onMounted(() => {
    token.value = localStorage.getItem("token");
    nombre.value = localStorage.getItem("nombre");
    cedula.value = localStorage.getItem("cedula");

    modalInstance.value = new Modal(exitoModal);
    modalErrorInstance.value = new Modal(errorModal);

    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }

    cargarDatos();
});
</script>
  
<style scoped>
.container {
    max-width: 100%;
    margin: 0 auto;
    padding: 0;
}
.header, .form-container {
    background-color: #ffffff;
    padding: 24px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    margin-top: 24px;
}
.header h1 {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 16px;
}
.search-input {
    width: 100%;
    padding: 8px;
    border: 1px solid #d1d5db;
    border-radius: 4px;
}
.form-container h2 {
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 16px;
}
.form-flex {
    display: flex;
    flex-wrap: wrap; /* Permite que los elementos se ajusten automáticamente */
    gap: 8px; /* Espaciado entre elementos */
    align-items: flex-start;
}
.form-group {
    flex: 1 1 calc(33.333% - 16px); /* Cada elemento ocupa 1/3 del ancho, restando el gap */
    min-width: 200px; /* Evita que se vuelvan demasiado pequeños */
    max-width: 500px; /* Máximo ancho para que no se expanda */
}
.form-group label {
    display: block;
    color: #4b5563;
    margin-bottom: 4px;
    font-size: 15px;
}

.duracion-container {
    display: flex;
    flex-direction: column;
}

.custom-select {
  position: relative;
  width: 100%;
  min-height: 37px;
  border-radius: 4px;
  padding: 10px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: white;
}
.selected-options {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
}
.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  border: 1px solid #ccc;
  background: white;
  z-index: 1000;
  max-height: 200px;
  overflow-y: auto;
}
.dropdown-item {
  padding: 8px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
}
.dropdown-item:hover {
  background: #f0f0f0;
}

.input-field {
    width: 100%;
    padding: 5px;
    border: 1px solid #d1d5db;
    border-radius: 4px;
    font-size: 14px;
}

.buttons {
    display: flex;
    gap: 8px;
    margin-top: 20px;
}
.buttons button {
    padding: 8px 16px;
    color: #ffffff;
    border: none;
    cursor: pointer;
}
.buttons .consultar {
    background-color: #5a4bbf;
    border-radius: 20px;
    transition: background 0.3s;
}
.buttons .consultar:hover {
    background-color: #6a5acd;
}
.buttons .exportar {
    background-color: #5a7acd;
    border-radius: 20px;
    transition: 0.4s;
}
.buttons .exportar:hover {
    background-color: #84a4f5;
}
.buttons .guardar {
    background-color: #191919;
    border-radius: 20px;
}
.buttons .guardar:hover {
    background-color: #616161;
}
.buttons .limpiar {
    background-color: #772017;
    border-radius: 20px;
    transition: 0.4s;
}
.buttons .limpiar:hover {
    background-color: #a73a2e;
}

.h3-title {
    font-size: 1.25rem;
    font-weight: bold;
}
/* Tabla */
.table-container {
    flex-grow: 1; /* Permite que la tabla ocupe todo el espacio restante */
    overflow-y: auto; /* Activa el scroll interno en la tabla */
    max-height: 100%; /* Se ajusta a la altura disponible */
}
table {
    width: 100%;
    border-collapse: collapse;

    position: relative;
}
/* Dejar fija la cabecera */
thead {
    position: sticky;
    top: 0;
    background-color: #e5e7eb; /* Fijar color de fondo para que no sea transparente */
    z-index: 10; /* Asegurar que esté sobre el contenido */
}
th, td {
    border: 1px solid #e5e7eb;
    padding: 8px;
    text-align: left;
    font-size: 0.875rem;
}
th {
    background-color: #e5e7eb;
}

/* Reducir el tamaño de los select dentro de la tabla */
.table-container table select {
    width: 80px; /* Ajusta el ancho */
    padding: 4px; /* Reduce el padding interno */
    font-size: 0.875rem; /* Hace el texto un poco más pequeño */
}

.table-container table input {
    width: 150px; /* Ajusta el ancho */
    padding: 4px; /* Reduce el padding interno */
    font-size: 0.875rem; /* Hace el texto un poco más pequeño */
}

/* Ajustar el tamaño del textarea dentro de la tabla */
.table-container table textarea {
    width: 100%; /* Ocupar todo el ancho de la celda */
    min-width: 200px; /* Asegurar un tamaño mínimo */
    height: 50px; /* Un poco más alto */
    resize: vertical; /* Permitir que el usuario lo ajuste en altura */
    font-size: 0.875rem;
}

.no-registros {
    text-align: center;
    font-weight: bold;
    color: #888;
    padding: 16px;
    font-size: 1rem;
}

.selected-row {
    transition: background-color 0.3s ease;
}

.selected-row:hover {
    background-color: #d4bdff; /* Un azul claro */
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Sombras suaves */
    transform: scale(1.01); /* Un ligero aumento de tamaño */
    cursor: pointer;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  gap: 2px;
  font-size: 14px; /* Reduce el tamaño de la fuente */
  padding: 8px;
}

.pagination label {
  margin-right: 10px;
  font-size: 0.9rem;
}

.pagination select {
  margin-right: 20px;
  padding: 4px;
  font-size: 0.8rem;
  height: 30px;
}

.pagination button {
  background-color: #5a4bbf;
  color: white;
  border: none;
  padding: 4px 8px;
  margin: 0 5px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 12px;
  height: 30px;
  transition: 0.3s;
}
.pagination button:hover {
  background-color: #6a5acd;
  color: white;
  border: none;
  padding: 4px 8px;
  margin: 0 5px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 12px;
  height: 30px;
}

.pagination button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.pagination span {
  margin: 0 10px;
  font-size: 0.9rem;
}

@media (max-width: 768px) {
    .form-group {
        flex: 1 1 calc(50% - 16px); /* 2 columnas en pantallas medianas */
    }
}

@media (max-width: 480px) {
    .form-group {
        flex: 1 1 100%; /* 1 columna en pantallas pequeñas */
    }
}
  
</style>
  