<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>
        <div class="container">
            <!-- Botón redondo flotante -->
            <button class="round-button" @click="mostrarFormModal = true" v-if="tipo_estado_formacion != 3">
                <i class="fa-solid fa-plus"></i>
            </button>
            <div class="form-container">
                <h2>{{ tema }}</h2>
                <form class="form-flex" @submit.prevent="actualizarFormacion">
                    <div class="form-group">
                        <label>Nivel de Formación:</label>
                        <p>{{ nivel_formacion }}</p>
                    </div>
                    <div class="form-group">
                        <label>Tipo de Actividad:</label>
                        <p>{{ tipo_actividad }}</p>
                    </div>
                    <div class="form-group">
                        <label>Objetivo General:</label>
                        <p>{{ objetivo_general }}</p>
                    </div>
                    <div class="form-group">
                        <label>Objetivos Específicos:</label>
                        <p>{{ objetivo_especifico }}</p>
                    </div>
                    <div class="form-group">
                        <label>Modalidad:</label>
                        <p>{{ modalidad }}</p>
                    </div>
                    <div class="form-group">
                        <label>Duración Horas:</label>
                        <p>{{ duracion_horas }}</p>
                    </div>
                    <div class="form-group">
                        <label>Duración Minutos:</label>
                        <p>{{ duracion_minutos }}</p>
                    </div>
                    <div class="form-group">
                        <label>Metodología y Contenido:</label>
                        <p>{{ metodologia }}</p>
                    </div>
                    <div class="form-group">
                        <label>Tipo:</label>
                        <p>{{ tipo }}</p>
                    </div>
                    <div class="form-group">
                        <label>Proveedor:</label>
                        <p>{{ proveedorBusqueda }}</p>
                    </div>
                    <div class="form-group">
                        <label>Evaluación:</label>
                        <p>{{ evaluacion }}</p>
                    </div>
                    <div class="form-group">
                        <label>Seguimiento y Retroalimentación:</label>
                        <p>{{ seguimiento }}</p>
                    </div>
                    <div class="form-group" v-if="tipo_estado_formacion != 3">
                        <label>Fecha de Inicio de Formación:</label>
                        <input type="date" class="input-field campo-reducido" v-model="fecha_inicio" :min="fechaActual">
                    </div>
                    <div class="form-group" v-else>
                        <label>Fecha de Inicio de Formación:</label>
                        <p>{{ fecha_inicio }}</p>
                    </div>
                    <div class="form-group" v-if="tipo_estado_formacion != 3">
                        <label>Fecha de Finalización de Formación:</label>
                        <input type="date" class="input-field campo-reducido" v-model="fecha_fin" :min="fecha_inicio">
                    </div>
                    <div class="form-group" v-else>
                        <label>Fecha de Finalización de Formación:</label>
                        <p>{{ fecha_fin }}</p>
                    </div>
                    <div class="form-group">
                        <label>Estado de la Formación:</label>
                        <div class="select-con-boton" v-if="validar_estado_formacion != 3">
                            <select class="input-field campo-reducido" v-model="tipo_estado_formacion">
                                <option :value="null">Seleccione...</option>
                                <option v-for="est in list_estados_formacion" :value="est.id">{{ est.nombre }}</option>
                            </select>
                            <button type="submit" class="submit-button">Actualizar</button>
                        </div>
                        <div class="select-con-boton" v-else>
                            <p>{{validar_estado_formacion_nombre}}</p>
                        </div>
                    </div>
                </form>
            </div>

            <div class="form-container">
                <div class="form-titulos">
                    <h2>ASIGNACIÓN DE PERSONAL</h2>
                    <h2>PERSONAL SELECCIONADO</h2>
                </div>

                <div class="contenedor-flex">
                    <div class="busqueda-y-combobox" v-if="tipo_estado_formacion != 3">
                        <!-- Campo de búsqueda -->
                        <input
                            type="text"
                            class="input-field"
                            v-model="busqueda"
                            placeholder="Buscar..."
                        />
                        <!-- Combobox (select múltiple) -->
                        <select
                            class="input-field combobox"
                            v-model="seleccionados"
                            multiple
                        >
                            <option v-for="persona in personalFiltrado" :value="persona" @click="seleccionarElementoIzquierda(persona)" :class="{ seleccionado: seleccionadoIzquierda?.cedula === persona.cedula }">
                                {{ persona.nombre }} - {{ persona.cedula }}
                            </option>
                        </select>
                    </div>
                    <div class="busqueda-y-combobox" v-else>
                        <!-- Campo de búsqueda -->
                        <input
                            type="text"
                            class="input-field"
                            v-model="busqueda"
                            placeholder="Buscar..."
                            readonly
                        />
                        <!-- Combobox (select múltiple) -->
                        <select
                            class="input-field combobox"
                            v-model="seleccionados"
                            multiple
                        >
                            <option v-for="persona in personalFiltrado" :value="persona" @click="seleccionarElementoIzquierda(persona)" :class="{ seleccionado: seleccionadoIzquierda?.cedula === persona.cedula }">
                                {{ persona.nombre }} - {{ persona.cedula }}
                            </option>
                        </select>
                    </div>

                    <div class="div-botones" v-if="tipo_estado_formacion != 3">
                        <!-- Botón para agregar seleccionados -->
                        <button class="submit-button inreract-btn" @click.prevent="agregarSeleccionados">
                            >
                        </button>

                        <!-- Botón para agregar todos -->
                        <button class="submit-button inreract-btn" @click.prevent="agregarTodos">
                            >>
                        </button>

                        <!-- Botón para agregar todos -->
                        <button class="submit-button inreract-btn" @click.prevent="limpiarSeleccionados">
                            <i class="fa-solid fa-trash"></i>
                        </button>
                    </div>

                    <!-- Segunda caja: Personal seleccionado -->
                    <div class="personal-seleccionado" v-if="tipo_estado_formacion != 3">
                        <div class="lista-seleccionados">
                            <ul>
                                <li v-for="(persona, index) in personalAgregado" :key="persona.cedula">
                                    {{ persona.nombre }} - {{ persona.cedula }}
                                    <button @click="eliminarSeleccionados(index)">❌</button>
                                </li>
                            </ul>
                        </div>
                    </div>
                    <div class="personal-seleccionado" v-else>
                        <div class="lista-seleccionados">
                            <ul>
                                <li v-for="(persona, index) in personalAgregado" :key="persona.cedula">
                                    {{ persona.nombre }} - {{ persona.cedula }}
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
                <button type="submit" class="guardar-button" @click="guardarPersonal" v-if="tipo_estado_formacion != 3">Guardar Personal</button>
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

        <!-- Modal adicional para el formulario -->
        <div class="modal-backdrop" v-if="mostrarFormModal"></div>
            
        <div class="custom-modal" v-if="mostrarFormModal">
            <div class="custom-modal-content large-modal">
                <div class="custom-modal-header">
                    <h5>Nuevo Formulario</h5>
                    <button @click="mostrarFormModal = false" class="custom-modal-close">×</button>
                </div>
                <div class="custom-modal-body">
                    <form @submit.prevent="actualizarMacroprocesos" class="two-column-form">
                        <!-- Columna Izquierda -->
                        <div class="form-column">
                            <div class="form-group">
                                <label>Macroproceso:</label>
                                <div class="custom-select" @click="toggleDropdown('macro')">
                                    <div class="selected-options">
                                        <span v-if="isAllSelectedMacro">TODOS ✓</span>
                                        <span v-else v-for="id in selected_macroproceso" :key="id">
                                            {{ getCompetenciaNombre(id, 'macro') }} ✓
                                        </span>
                                    </div>
                                    <div class="dropdown" v-if="dropdownVisibleMacro" @click.stop>
                                        <div class="dropdown-item" :class="{ 'selected': isAllSelectedMacro }" @click.stop="toggleAll('macro')">
                                            TODOS <span v-if="isAllSelectedMacro">✓</span>
                                        </div>
                                        <div v-for="macro in list_macroprocesos" 
                                            :key="macro.id" 
                                            class="dropdown-item" 
                                            :class="{ 'selected': selected_macroproceso.includes(macro.id) }"
                                            @click.stop="toggleSelection(macro.id, 'macro')">
                                            {{ macro.nombre }}
                                            <span v-if="selected_macroproceso.includes(macro.id)">✓</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                        </div>
                        
                        <!-- Columna Derecha -->
                        <div class="form-column">
                            <div class="form-group">
                                <label>Público Objetivo:</label>
                                <div class="custom-select" @click="toggleDropdown('opciones')">
                                    <div class="selected-options">
                                        <span v-if="isAllSelectedOpciones">TODOS ✓</span>
                                        <span v-else v-for="id in selected_opciones" :key="id">
                                            {{ getCompetenciaNombre(id, 'opcion') }} ✓
                                        </span>
                                    </div>
                                    <div class="dropdown" v-if="dropdownVisibleOpciones" @click.stop>
                                        <div class="dropdown-item" :class="{ 'selected': isAllSelectedOpciones }" @click.stop="toggleAll('opciones')">
                                            TODOS <span v-if="isAllSelectedOpciones">✓</span>
                                        </div>
                                        <div v-for="(cargos, macroNombre) in list_opciones" :key="macroNombre">
                                            <div class="dropdown-header"><b>{{ macroNombre }}</b></div>
                                            <div 
                                                v-for="cargo in cargos" 
                                                :key="cargo.id" 
                                                class="dropdown-item" 
                                                :class="{ 'selected': selected_opciones.includes(cargo.id) }" 
                                                @click.stop="toggleSelection(cargo.id, 'opcion')"
                                            >
                                                {{ cargo.nombre }} <span v-if="selected_opciones.includes(cargo.id)">✓</span>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
                            <div class="form-group">
                                <label id="label-blanco">Macroproceso:</label>
                            </div>
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
import { ref, onMounted, watch, computed } from "vue";
import { useRouter, useRoute } from "vue-router";
import axios from "axios";
import { Modal } from 'bootstrap';

const token = ref("");
const nombre = ref("");
const cedula = ref("");

const formacion_id = ref("");

const nivel_formacion = ref(null);
const tipo_actividad = ref(null);
const tipo_estado_formacion = ref(null);
const tema = ref("");
const origen = ref("");
const objetivo_general = ref("");
const objetivo_especifico = ref("");
const modalidad = ref(null);
const duracion_horas = ref(0);
const duracion_minutos = ref(0);
const metodologia = ref("");
const tipo = ref(null);
const ciudad = ref(null);
const evaluacion = ref("");
const seguimiento = ref("");
const fecha_inicio = ref(null);
const fecha_fin = ref(null);
const fechaActual = ref(null);
const selected_macroproceso = ref([]);
const selected_opciones = ref([]);
const list_opciones = ref({});
const list_macroprocesos = ref([]);
const validar_estado_formacion = ref(null);
const validar_estado_formacion_nombre = ref('');

const list_estados_formacion = ref([]);
const proveedorBusqueda = ref("");

const modalInstance = ref(null);
const modalErrorInstance = ref(null);
const token_status = ref(0);

const seleccionadoIzquierda = ref(null);

const msg = ref("");
const errorMsg = ref("");

const dropdownVisibleMacro = ref(false);
const dropdownVisibleOpciones = ref(false);

const mostrarFormModal = ref(false);

// Variables reactivas
const busqueda = ref(""); // Para almacenar el texto de búsqueda
const list_personal = ref([]);
const seleccionados = ref([]); // Para almacenar los elementos seleccionados
const personalFiltrado = ref([]); // Para almacenar los países filtrados
const personalAgregado = ref([]); // Para almacenar el personal agregado a la segunda caja

const router = useRouter();
const route = useRoute();

// ✅ Función que actualiza los macroprocesos elegidos
const actualizarMacroprocesos = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }

        const response = await axios.post(
            `${apiUrl}/actualizar_macroprocesos`, 
            {
                formacion_id: formacion_id.value,
                lista_macroprocesos: selected_macroproceso.value,
                lista_cargos: selected_opciones.value,
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
            mostrarFormModal.value = false;
            // modalInstance.value.show();
            await getFormacion();
            await cargarDatos();
            await cargarSelects();
            await getPersonalFormacion();
        }

    } catch (error) {
        console.error('Error al actualizar macroprocesos:', error);
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

// Método para seleccionar un elemento en la primera caja
const seleccionarElementoIzquierda = (persona) => {
    seleccionadoIzquierda.value = persona;
};

// Método para agregar todos los elementos de la caja izquierda a la derecha
const agregarTodos = () => {
    // Filtramos para evitar duplicados
    const nuevosSeleccionados = personalFiltrado.value.filter(
        persona => !personalAgregado.value.some(p => p.cedula === persona.cedula)
    );
    personalAgregado.value = [...personalAgregado.value, ...nuevosSeleccionados];
};

// Método para limpiar la caja derecha
const limpiarSeleccionados = () => {
    personalAgregado.value = [];
};

// ✅ Función que llama a la api para guardar la formación
const getFormacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/get_formacion_by_id`, 
            {
                formacion_id: formacion_id.value,
            },
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            nivel_formacion.value = response.data.data.nivel_formacion_nombre;
            tipo_actividad.value = response.data.data.tipo_actividad_nombre;
            tema.value = response.data.data.tema.toUpperCase();
            origen.value = capitalizarPrimeraLetra(response.data.data.origen);
            objetivo_general.value = capitalizarPrimeraLetra(response.data.data.objetivo_general);
            objetivo_especifico.value = capitalizarPrimeraLetra(response.data.data.objetivo_especifico);
            modalidad.value = response.data.data.modalidad_nombre;
            duracion_horas.value = response.data.data.duracion_horas;
            duracion_minutos.value = response.data.data.duracion_minutos;
            metodologia.value = capitalizarPrimeraLetra(response.data.data.metodologia);
            tipo.value = response.data.data.tipo_nombre;
            proveedorBusqueda.value = response.data.data.proveedor_nombre;
            ciudad.value = response.data.data.ciudad;
            evaluacion.value = capitalizarPrimeraLetra(response.data.data.evaluacion);
            seguimiento.value = capitalizarPrimeraLetra(response.data.data.seguimiento);
            tipo_estado_formacion.value = response.data.data.estado_formacion;
            fecha_inicio.value = response.data.data.fecha_inicio;
            fecha_fin.value = response.data.data.fecha_fin;
            validar_estado_formacion.value = response.data.data.estado_formacion;
            validar_estado_formacion_nombre.value = response.data.data.estado_formacion_nombre;
        }

    } catch (error) {
        console.error('Error al obtener formación:', error);
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

// ✅ Función que llama a la api para guardar la formación
const actualizarFormacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/actualizar_formacion`, 
            {
                formacion_id: formacion_id.value,
                estado_formacion: tipo_estado_formacion.value,
                fecha_inicio: fecha_inicio.value,
                fecha_fin: fecha_fin.value,
            },
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            await getFormacion();
            await cargarDatos();
            await cargarSelects();
            await getPersonalFormacion();
            msg.value = "Formación actualizada exitosamente.";
            modalInstance.value.show();
        }

    } catch (error) {
        console.error('Error al actualizar formación:', error);
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
            `${apiUrl}/get_formacion_estados`, 
            {
                formacion_id: parseInt(formacion_id.value)
            },
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            list_estados_formacion.value = response.data.data.estados;
            list_personal.value = response.data.data.personal;
            personalFiltrado.value = list_personal.value;
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

// ✅ Función para cargar el personal anteriormente guardado
const getPersonalFormacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/obtener_personal_seleccionado_formacion`, 
            {
                formacion_id: formacion_id.value
            },
            {
                headers: {
                    Accept: "application/json",
                    Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            personalAgregado.value = response.data.data;
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

const capitalizarPrimeraLetra = (str) => {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

// Función para obtener la fecha actual en formato YYYY-MM-DD
const obtenerFechaActual = () => {
  const hoy = new Date();
  const año = hoy.getFullYear();
  const mes = String(hoy.getMonth() + 1).padStart(2, '0'); // Los meses van de 0 a 11
  const dia = String(hoy.getDate()).padStart(2, '0');
  return `${año}-${mes}-${dia}`;
};

// Método para filtrar el personal según la búsqueda
const filtrarPersonal = () => {
    if (!busqueda.value) {
        personalFiltrado.value = list_personal.value; // Si no hay búsqueda, muestra todo el personal
    } else {
        personalFiltrado.value = list_personal.value.filter(persona =>
            `${persona.nombre} - ${persona.cedula}`.toLowerCase().includes(busqueda.value.toLowerCase())
        );
    }
};

// Método para agregar los elementos seleccionados
const agregarSeleccionados = () => {
    // Filtramos para evitar duplicados
    const nuevosSeleccionados = seleccionados.value.filter(
        persona => !personalAgregado.value.some(p => p.cedula === persona.cedula)
    );
    personalAgregado.value = [...personalAgregado.value, ...nuevosSeleccionados];
    seleccionados.value = []; // Limpiamos la selección
};

// Método para eliminar un elemento de la segunda caja
const eliminarSeleccionados = (index) => {
    personalAgregado.value.splice(index, 1);
};

// Watch para filtrar automáticamente cuando cambia la búsqueda
watch(busqueda, filtrarPersonal);

const guardarPersonal = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/guardar_personal_formacion`, 
            {
                formacion_id: formacion_id.value,
                personal: personalAgregado.value
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
            modalInstance.value.show();
        }

    } catch (error) {
        console.error('Error al actualizar formación:', error);
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

const toggleDropdown = (type) => {
    if (type == 'macro'){
        dropdownVisibleMacro.value = !dropdownVisibleMacro.value;
    }else if (type == 'opciones') {
        dropdownVisibleOpciones.value = !dropdownVisibleOpciones.value;   
    } 
};

const toggleSelection = (id, type) => {
    if (type == 'macro'){
        const index4 = selected_macroproceso.value.indexOf(id);
        if (index4 === -1) {
          selected_macroproceso.value.push(id);
        } else {
          selected_macroproceso.value.splice(index4, 1);
        }
        selected_macroproceso.value = [...selected_macroproceso.value];
    } else if (type == 'opcion') {
        const index5 = selected_opciones.value.indexOf(id);
        if (index5 === -1) {
            selected_opciones.value.push(id);
        } else {
            selected_opciones.value.splice(index5, 1);
        } 
    }
};

const getCompetenciaNombre = (id, type) => {
    if (type == 'macro'){
        const macro = list_macroprocesos.value.find(m => m.id === id);
        return macro ? macro.nombre : "";
    }else if (type == 'opcion') {
        for (const key in list_opciones.value) {
            const opcion = list_opciones.value[key].find(o => o.id === id);
            if (opcion) return opcion.nombre;
        }
        return "";
    }
};

// Función para seleccionar/deseleccionar todas las opciones
const toggleAll = (type) => {
    if (type == 'macro'){
        if (isAllSelectedMacro.value) {
            selected_macroproceso.value = []; // Si ya estaban todas, vaciar
        } else {
            selected_macroproceso.value = list_macroprocesos.value.map(m => m.id); // Seleccionar todas
        }
    } else if (type == 'opciones') {
        const allOpciones = Object.values(list_opciones.value).flat().map(o => o.id);
        selected_opciones.value = isAllSelectedOpciones.value ? [] : allOpciones;
    }
};

watch(selected_macroproceso, (newVal, oldVal) => {
    cargarOpcionesRelacionadas();
});

const cargarOpcionesRelacionadas = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        if (!selected_macroproceso.value.length) {
            list_opciones.value = {};
            return;
        }
        const response = await axios.post(`${apiUrl}/get_cargos_por_macroproceso`, 
            { 
                macroprocesos: selected_macroproceso.value 
            }, 
            {
                headers: { 
                    Accept: "application/json", Authorization: `Bearer ${token.value}`
                }
            }
        );
        if (response.status === 200) {
            // list_opciones.value = { ...response.data.data };
            const datos = response.data.data;

            // Reestructuramos el objeto agrupando por macroproceso
            list_opciones.value = datos.reduce((acc, macro) => {
                acc[macro.macro_nombre] = macro.cargos.map(cargo => ({
                    id: cargo.cargo_id,
                    nombre: cargo.cargo_nombre
                }));
                return acc;
            }, {});
        }
    } catch (error) {
        console.error('Error al cargar las opciones:', error);
    }
};

const isAllSelectedMacro = computed(() => selected_macroproceso.value.length === list_macroprocesos.value.length);
const isAllSelectedOpciones = computed(() => {
    if (selected_opciones.value.length === 0) return false;
    const allOpciones = Object.values(list_opciones.value).flat().map(o => o.id);
    return selected_opciones.value.length === allOpciones.length;
});

// ✅ Función para cargar los select generales del formulario
const cargarSelects = async () => {
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
            list_macroprocesos.value = response.data.data.macroprocesos;
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

// ✅ Función mounted que carga información ANTES de que la página renderice
onMounted(() => {
    token.value = localStorage.getItem("token");
    nombre.value = localStorage.getItem("nombre");
    cedula.value = localStorage.getItem("cedula");

    formacion_id.value = parseInt(route.params.id); // Capturar el ID de la URL
    fechaActual.value = obtenerFechaActual();

    modalInstance.value = new Modal(exitoModal);
    modalErrorInstance.value = new Modal(errorModal);

    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }
    getFormacion();
    cargarDatos();
    cargarSelects();
    getPersonalFormacion();
});
</script>
  
<style scoped>
.container {
    max-width: 100%;
    margin: 0 auto;
    padding: 0;
}

/* Botón redondo */
.round-button {
    position: fixed;
    right: 30px;
    top: 100px;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background-color: #6a5acd;
    color: white;
    border: none;
    font-size: 24px;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s ease;
}

.round-button:hover {
    background-color: #5a4bbf;
    transform: scale(1.1);
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

/* Formulario de dos columnas */
.two-column-form {
    display: flex;
    gap: 30px;
    flex-wrap: wrap;
}

.form-column {
    flex: 1;
    min-width: 400px;
}

/* Asegúrate que los inputs dentro de la modal tengan buen espaciado */
.custom-modal .form-group {
    margin-bottom: 20px;
}

.custom-modal .input-field {
    width: 100%;
    padding: 10px;
    margin-top: 8px;
    font-size: 15px;
}

.custom-select {
  position: relative;
  width: 100%;
  min-height: 45px;
  border-radius: 4px;
  padding: 10px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: white;
  margin-top: 8px;
  font-size: 14px;
}
.selected-options {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
  min-height: 25px;
}
.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  border: 1px solid #ccc;
  background: white;
  z-index: 1100;
  max-height: 300px;
  overflow-y: auto;
  margin-top: 5px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
.dropdown-item {
  padding: 10px 15px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.dropdown-item:hover {
  background: #f0f0f0;
}

.selected {
    background-color: #5a4bbf;
    color: white;
    font-weight: bold;
    transition: 0.4s;
}

.selected:hover {
    background-color: #afa9da;
    color: white;
    font-weight: bold;
}

#label-blanco {
    color: white !important;
}

.header, .form-container {
    background-color: #ffffff;
    padding: 24px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    margin-top: 14px;
}
.header h1 {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 16px;
}
.form-container h2 {
    font-size: 18px;
    font-weight: bold;
    margin-bottom: 16px;
}
.form-flex {
    display: flex;
    flex-wrap: wrap; /* Permite que los elementos se ajusten automáticamente */
    gap: 14px; /* Espaciado entre elementos */
    align-items: flex-start;
}
.form-flex {
    display: flex;
    flex-wrap: wrap; /* Permite que los elementos se ajusten automáticamente */
    gap: 4px; /* Espaciado entre elementos */
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
    font-weight: bold;
    font-size: 15px;
}

.form-group p {
    font-size: 14px;
}

.input-field {
    width: 100%;
    padding: 8px;
    border: 1px solid #e7e7e7;
    border-radius: 4px;
    font-size: 14px;
}

.select-con-boton {
    display: flex; /* Alinea el select y el botón en la misma fila */
    align-items: center; /* Centra verticalmente los elementos */
    gap: 8px; /* Espacio entre el select y el botón */
}

.submit-button {
    width: 120px;
    height: 35px;
    margin-left: 20px;
    padding: 8px;
    background: #6a5acd;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s;
    font-size: 0.9em;
}
.guardar-button {
    width: 100%;
    height: 35px;
    margin-top: 10px;
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

.campo-reducido {
    width: 70%; /* Reduce el ancho al 70% del contenedor */
    max-width: 270px; /* Establece un ancho máximo para evitar que se vean demasiado pequeños */
}

.form-titulos {
    display: flex;
    justify-content: space-around;
}

.contenedor-flex {
    display: flex;
    gap: 20px; /* Espacio entre las dos cajas */
}

.busqueda-y-combobox, .personal-seleccionado {
    flex: 1; /* Ambas cajas ocupan el mismo espacio */
    max-width: 50%;
}

.combobox {
    height: 300px; /* Altura del combobox */
    resize: vertical; /* Permite redimensionar verticalmente */
}

/* Estilos para la primera caja */
.combobox option {
    padding: 8px;
    cursor: pointer; /* Cambia el cursor al pasar sobre el elemento */
    transition: background 0.3s; /* Transición suave para el efecto hover */
}

/* Efecto hover en la primera caja */
.combobox option:hover {
    background-color: #e6e6fa; /* Color lila claro */
}

/* Efecto de selección en la primera caja */
.combobox option.seleccionado {
    background-color: #9370db; /* Color lila más oscuro */
    color: white; /* Texto en blanco para mejor contraste */
}

.div-botones {
    display: flex;
    flex-direction: column;
    margin-top: 10px;
}

.lista-seleccionados {
    height: 340px; /* Alto fijo para el contenedor */
    overflow-y: auto; /* Habilita el scroll vertical */
    border: 1px solid #e7e7e7; /* Borde sutil */
    border-radius: 4px; /* Bordes redondeados */
    padding: 8px; /* Espaciado interno */
}

.lista-seleccionados ul {
    list-style-type: none;
    padding: 0;
    margin: 0; /* Elimina el margen predeterminado */
}

.lista-seleccionados li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px;
    border-bottom: 1px solid #e7e7e7; /* Línea divisoria entre elementos */
    font-size: 14px;
    cursor: pointer; /* Cambia el cursor al pasar sobre el elemento */
    transition: background 0.3s; /* Transición suave para el efecto hover */
}

/* Efecto hover */
.lista-seleccionados li:hover {
    background-color: #9370db;
    color: white;
}

.lista-seleccionados li:last-child {
    border-bottom: none; /* Elimina la línea divisoria del último elemento */
}

.personal-seleccionado ul {
    list-style-type: none;
    padding: 0;
}

.personal-seleccionado li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 2px;
    border: 0 solid #e7e7e7;
    border-radius: 4px;
    margin-bottom: 2px;
    font-size: 14px;
}

.personal-seleccionado button {
    background: none;
    border: none;
    cursor: pointer;
    color: #ff4d4d;
    font-size: 14px;
}

.inreract-btn{
    margin-right: 20px;
    width: 50px;
    margin-top: 10px;
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
  