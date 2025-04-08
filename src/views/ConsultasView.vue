<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>
        <div class="container">
            <div class="form-container">
                <h2>Consultar</h2>
                <form class="form-flex" @submit.prevent="consultarDatos">
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
                    <div class="form-group">
                    </div>
                    <div class="form-group">
                    </div>
                    <div class="form-group">
                        <div class="buttons">
                            <button type="submit" class="consultar">Consultar</button>
                            <button type="button" class="limpiar" @click="limpiar">Limpiar</button>
                        </div>
                    </div>
                </form>
            </div>
            <div class="form-container">
                <div class="table-container">
                    <h3 class="h3-title">FORMACIONES</h3>
                    <table>
                        <thead>
                            <tr>
                                <th>CÓDIGO</th>
                                <th>TEMA</th>
                                <th>NIVEL DE FORMACIÓN</th>
                                <th>TIPO DE ACTIVIDAD</th>
                                <th>MODALIDAD</th>
                                <th>DURACIÓN</th>
                                <th>ESTADO</th>
                                <th>MACROPROCESO</th>
                                <th>PERSONAL</th>
                                <th>NOTAS</th>
                                <th>FECHA INICIO</th>
                                <th>FECHA FIN</th>
                                <th>CALIFICAR</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-if="lista_registros.length === 0">
                                <td colspan="13" class="no-registros">No hay registros disponibles</td>
                            </tr>
                            <tr v-else v-for="(reg, index) in lista_registros" :key="index" @mouseover="selectRow(index)" :class="{ 'selected-row': index === selectedRowId }">
                                <td>{{ reg.codigo }}</td>
                                <td>{{ reg.tema }}</td>
                                <td>{{ reg.nivel_formacion }}</td>
                                <td>{{ reg.tipo_actividad }}</td>
                                <td>{{ reg.modalidad }}</td>
                                <td>{{ reg.duracion }}</td>
                                <td>{{ reg.estado_formacion }}</td>
                                <td>{{ reg.macroproceso }}</td>
                                <td>{{ reg.nombre }}</td>
                                <td>{{ reg.resumen_notas }}</td>
                                <td>{{ reg.fecha_inicio }}</td>
                                <td>{{ reg.fecha_fin }}</td>
                                <td style="text-align: center;" @click="calificar(reg)"><i class="fa-regular fa-clipboard"></i></td>
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
                        {{ msgExito }}
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

        <!-- Modal adicional para el formulario -->
        <div class="modal-backdrop" v-if="mostrarFormModal"></div>
            
        <div class="custom-modal" v-if="mostrarFormModal">
            <div class="custom-modal-content large-modal">
                <div class="custom-modal-header">
                    <h5>{{selected_tema}}</h5>
                    <button @click="mostrarFormModal = false" class="custom-modal-close">×</button>
                </div>
                <div class="custom-modal-body">
                    <form class="form-flex form-modal" @submit.prevent="guardarCalificacion">
                        <div class="form-group" v-if="selected_list_evaluacion.includes(1)">
                            <label for="cali_escrita">Evaluación Escrita:</label>
                            <input type="text" id="cali_escrita" @input="limitarDigitos($event, 1)" placeholder="Nota" min="0" max="5" v-model="cal_escrita" required>
                        </div>
                        <div class="form-group" v-if="selected_list_evaluacion.includes(2)">
                            <label for="cali_ejercicios_practicos">Evaluación Ejercicios Prácticos o Estudio de Casos:</label>
                            <input type="text" id="cali_ejercicios_practicos" @input="limitarDigitos($event, 2)" placeholder="Nota" min="0" max="5" v-model="cal_practica" required>
                        </div>
                        <div class="form-group" v-if="selected_list_evaluacion.includes(3)">
                            <label for="cali_interactiva">Evaluación Interactiva:</label>
                            <input type="text" id="cali_interactiva" @input="limitarDigitos($event, 3)" placeholder="Nota" min="0" max="5" v-model="cal_interactiva" required>
                        </div>
                        <!-- Botón de submit -->
                        <div class="form-submit">
                            <button type="submit" class="submit-button">Guardar</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <!-- Efecto de desenfoque cuando la modal está abierta -->
        <div class="blur-overlay" v-if="mostrarFormModal"></div>

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
const mostrarFormModal = ref(false);

const token_status = ref(0);

const msg = ref("");
const msgExito = ref("");
const errorMsg = ref("");

const selected_formacionId = ref(null);
const selected_cedula = ref(null);
const selected_tema = ref(null);
const selected_nombre = ref(null);
const selected_list_evaluacion = ref([]);

const cal_escrita = ref(null);
const cal_practica = ref(null);
const cal_interactiva = ref(null);

const router = useRouter();


// ✅ Función para cargar los datos a la tabla
const consultarDatos = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }

        if (codigo.value) {
            codigo.value = codigo.value.toUpperCase();
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
    usuarioId.value = '';
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
    if (nuevoValor.length == 0){
        nuevoValor = ''
        usuarioId.value = ''
    }
});

// ✅ Función para cambiar pagina del paginador
const changePage = async (newPosition) => {
  position.value = newPosition;
  await consultarDatos(); // Vuelve a cargar los datos con el nuevo límite y posición
};

// ✅ Función para cambiar pagina del paginador
const calificar = async (reg) => {
    selected_formacionId.value = reg.id;
    selected_cedula.value = reg.cedula;
    selected_tema.value = reg.tema;
    selected_nombre.value = reg.nombre;
    selected_list_evaluacion.value = reg.evaluacion;
    mostrarFormModal.value = true;
    cal_escrita.value = reg.nota_eva_escrita;
    cal_practica.value = reg.nota_eva_practica;
    cal_interactiva.value = reg.nota_eva_interactiva;
};

// ✅ Función para guardar la calificación de persona por formación
const guardarCalificacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }

        const response = await axios.post(
            `${apiUrl}/guardar_calificacion`, 
            {
                formacion_id: selected_formacionId.value,
                cedula: selected_cedula.value,
                nota_eva_escrita: cal_escrita.value,
                nota_eva_practica: cal_practica.value,
                nota_eva_interactiva: cal_interactiva.value
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
            msgExito.value = msg.value;
            mostrarFormModal.value = false;
            modalInstance.value.show();
            await consultarDatos();
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

// ✅ Función para limitar los digitos de los campos de calificación a 5
const limitarDigitos = (event, tipo) => {
    let valor = event.target.value;

    // Reemplazar cualquier carácter que no sea dígito o punto
    valor = valor.replace(/[^0-9.]/g, '');

    // Solo permitir un punto decimal
    const partes = valor.split('.');
    if (partes.length > 2) {
        valor = partes[0] + '.' + partes[1];
    }

    // Limitar a un solo decimal
    if (partes.length === 2) {
        partes[1] = partes[1].slice(0, 1); // solo un decimal
        valor = partes[0] + '.' + partes[1];
    }

    // Convertir a número y validar rango máximo 5.0
    let numero = parseFloat(valor);
    if (numero > 5) {
        valor = '5.0';
    }

    event.target.value = valor;

    if (tipo === 1) {
        cal_escrita.value = valor;
    } else if (tipo === 2) {
        cal_practica.value = valor;
    } else {
        cal_interactiva.value = valor;
    }
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

/* Efecto de desenfoque */
.blur-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(5px);
    z-index: 1040; /* Por encima de todo pero debajo de la modal */
}

.modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(5px);
    z-index: 1040;
}

/* Modal grande */
.large-modal {
    width: 900px !important;
    max-width: 95% !important;
    height: auto;
    max-height: 90vh;
}

.custom-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1050;
}

.custom-modal-content {
    background: white;
    border-radius: 8px;
    width: 850px;
    max-width: 92%;
    max-height: 90vh;
    height: auto;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
    overflow: hidden;
}

.custom-modal-header {
    padding: 16px 24px;
    background: #6a5acd;
    color: white;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.custom-modal-header h5 {
    margin: 0;
    font-size: 1.25rem;
}

.custom-modal-close {
    background: none;
    border: none;
    color: white;
    font-size: 1.6rem;
    cursor: pointer;
    padding: 0;
}

.custom-modal-body {
    padding: 25px;
    overflow-y: auto;
}

.submit-button {
    width: 120px;
    height: 35px;
    margin-top: 20px;
    padding: 8px;
    background: #6a5acd;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s;
    font-size: 0.9em;
}
.submit-button:hover, .guardar-button:hover {
    background: #5a4bbf;
}

.form-modal {
    display: flex;
    flex-direction: column;
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
  