<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>

        <div class="container">
            <div class="form-container">
                <h2>Registrar Nueva Formación</h2>
                <form @submit.prevent="guardarFormacion" class="form-flex">
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
                        <label>Tema:</label>
                        <input type="text" class="input-field" v-model="tema" required>
                    </div>
                    <div class="form-group">
                        <label>Origen de la Necesidad:</label>
                        <input type="text" class="input-field" v-model="origen" required>
                    </div>
                    <div class="form-group">
                        <label>Objetivo General:</label>
                        <input type="text" class="input-field" v-model="objetivo_general" required>
                    </div>
                    <div class="form-group">
                        <label>Objetivos Específicos:</label>
                        <input type="text" class="input-field" v-model="objetivo_especifico" required>
                    </div>
                    <div class="form-group">
                        <label>Competencias Corporativas:</label>
                        <div class="custom-select" @click="toggleDropdown('corp')">
                            <div class="selected-options" >
                                <span v-if="isAllSelected">TODOS ✓</span>
                                <span v-else v-for="id in selected_competencia_corporativa" :key="id">
                                {{ getCompetenciaNombre(id, 'corp') }} ✓
                                </span>
                            </div>
                             <!-- Dropdown de opciones -->
                            <div class="dropdown" v-if="dropdownVisibleCorp" @click.stop>
                                <!-- Opción "TODOS" -->
                                <div class="dropdown-item" :class="{ 'selected': isAllSelected }" @click.stop="toggleAll('corp')">
                                TODOS
                                <span v-if="isAllSelected">✓</span>
                                </div>

                                <!-- Opciones dinámicas -->
                                <div v-for="compe_corp in list_competencia_corporativa" 
                                    :key="compe_corp.id" 
                                    class="dropdown-item" 
                                    :class="{ 'selected': selected_competencia_corporativa.includes(compe_corp.id) }"
                                    @click.stop="toggleSelection(compe_corp.id, 'corp')">
                                {{ compe_corp.orden }}. {{ compe_corp.nombre }}
                                <span v-if="selected_competencia_corporativa.includes(compe_corp.id)">✓</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>Competencias de Rol:</label>
                        <div class="custom-select" @click="toggleDropdown('rol')">
                            <div class="selected-options">
                                <span v-if="isAllSelectedRol">TODOS ✓</span>
                                <span v-else v-for="id in selected_competencia_rol" :key="id">
                                {{ getCompetenciaNombre(id, 'rol') }} ✓
                                </span>
                            </div>
                             <!-- Dropdown de opciones -->
                            <div class="dropdown" v-if="dropdownVisibleRol">
                                <!-- Opción "TODOS" -->
                                <div class="dropdown-item" :class="{ 'selected': isAllSelectedRol }"  @click.stop="toggleAll('rol')">
                                TODOS
                                <span v-if="isAllSelectedRol">✓</span>
                                </div>

                                <!-- Opciones dinámicas -->
                                <div v-for="compe_rol in list_competencia_rol" 
                                    :key="compe_rol.id" 
                                    class="dropdown-item"
                                    :class="{ 'selected': selected_competencia_rol.includes(compe_rol.id) }"
                                    @click.stop="toggleSelection(compe_rol.id, 'rol')">
                                {{ compe_rol.orden }}. {{ compe_rol.nombre }}
                                <span v-if="selected_competencia_rol.includes(compe_rol.id)">✓</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>Competencias de Posición:</label>
                        <div class="custom-select" @click="toggleDropdown('pos')">
                            <div class="selected-options">
                                <span v-if="isAllSelectedPos">TODOS ✓</span>
                                <span v-else v-for="id in selected_competencia_posicion" :key="id">
                                {{ getCompetenciaNombre(id, 'pos') }} ✓
                                </span>
                            </div>
                             <!-- Dropdown de opciones -->
                            <div class="dropdown" v-if="dropdownVisiblePos">
                                <!-- Opción "TODOS" -->
                                <div class="dropdown-item" :class="{ 'selected': isAllSelectedPos }"  @click.stop="toggleAll('pos')">
                                TODOS
                                <span v-if="isAllSelectedPos">✓</span>
                                </div>

                                <!-- Opciones dinámicas -->
                                <div v-for="compe_pos in list_competencia_posicion" 
                                    :key="compe_pos.id" 
                                    class="dropdown-item" 
                                    :class="{ 'selected': selected_competencia_posicion.includes(compe_pos.id) }"
                                    @click.stop="toggleSelection(compe_pos.id, 'pos')">
                                {{ compe_pos.orden }}. {{ compe_pos.nombre }}
                                <span v-if="selected_competencia_posicion.includes(compe_pos.id)">✓</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>Modalidad:</label>
                        <select class="input-field" v-model="modalidad">
                            <option :value="null">Seleccione...</option>
                            <option v-for="tip_mod in list_tipo_modalidad" :value="tip_mod.id">{{ tip_mod.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Duración Horas:</label>
                        <input type="number" class="input-field" v-model="duracion_horas" required>
                    </div>
                    <div class="form-group">
                        <label>Duración Minutos:</label>
                        <input type="number" class="input-field" v-model="duracion_minutos" required>
                    </div>
                    <div class="form-group">
                        <label>Metodología y Contenido:</label>
                        <input type="text" class="input-field" v-model="metodologia" required>
                    </div>
                    <div class="form-group">
                        <label>Tipo:</label>
                        <select class="input-field" v-model="tipo">
                            <option :value="null">Seleccione...</option>
                            <option :value="1">INTERNO</option>
                            <option :value="2">EXTERNO</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Proveedor:</label>
                        <input 
                            type="text" 
                            class="input-field" 
                            v-model="proveedorBusqueda" 
                            @focus="mostrarLista = true" 
                            @blur="ocultarLista"
                            placeholder="Escriba el proveedor..."
                        >
                        <ul v-if="mostrarLista && proveedores.length" class="dropdown-list">
                            <li v-for="prov in proveedores" :key="prov.id" @mousedown="seleccionarProveedor(prov)">
                                {{ prov.nombres }} - {{ prov.nit }}
                            </li>
                        </ul>
                    </div>
                    <div class="form-group">
                        <label>Macroproceso:</label>
                        <div class="custom-select" @click="toggleDropdown('macro')">
                            <div class="selected-options" >
                                <span v-if="isAllSelectedMacro">TODOS ✓</span>
                                <span v-else v-for="id in selected_macroproceso" :key="id">
                                {{ getCompetenciaNombre(id, 'macro') }} ✓
                                </span>
                            </div>
                             <!-- Dropdown de opciones -->
                            <div class="dropdown" v-if="dropdownVisibleMacro" @click.stop>
                                <!-- Opción "TODOS" -->
                                <div class="dropdown-item" :class="{ 'selected': isAllSelectedMacro }" @click.stop="toggleAll('macro')">
                                TODOS
                                <span v-if="isAllSelectedMacro">✓</span>
                                </div>

                                <!-- Opciones dinámicas -->
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
                        <label>Público Objetivo:</label>
                        <select class="input-field" v-model="publico_objetivo">
                            <option :value="null">Seleccione...</option>
                            <option>Presencial</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Ciudad:</label>
                        <select class="input-field" v-model="ciudad">
                            <option :value="null">Seleccione...</option>
                            <option v-for="ciudad in list_ciudades_formacion" :value="ciudad.id">{{ ciudad.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Evaluación:</label>
                        <input type="text" class="input-field" v-model="evaluacion" required>
                    </div>
                    <div class="form-group">
                        <label>Seguimiento y Retroalimentación:</label>
                        <input type="text" class="input-field" v-model="seguimiento" required>
                    </div>
                    <div class="form-group">
                        <label>Fecha de Inicio de Formación:</label>
                        <input type="date" class="input-field" v-model="fecha_inicio">
                    </div>
                    <div class="form-group">
                        <label>Fecha de Finalización de Formación:</label>
                        <input type="date" class="input-field" v-model="fecha_fin" :min="fecha_inicio">
                    </div>
                    <button type="submit" class="submit-button">Registrar Formación</button>
                </form>
            </div>
        </div>

        <!-- Modal de éxito -->
        <div class="modal fade" id="exitoModal" tabindex="-1" aria-labelledby="exitoModalLabel" aria-hidden="true" data-bs-backdrop="static" ref="exitoModal">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exitoModalLabel">Modal Formación</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close" @click="goListaReportes"></button>
                    </div>
                    <div class="modal-body">
                        {{ msg }}
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="limpiar">Cerrar</button>
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
import { ref, onMounted, watch, computed } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import { Modal } from 'bootstrap';

const token = ref("");
const nombre = ref("");
const cedula = ref("");

const dropdownVisibleCorp = ref(false);
const dropdownVisibleRol = ref(false);
const dropdownVisiblePos = ref(false);
const dropdownVisibleMacro = ref(false);

const nivel_formacion = ref(null);
const tipo_actividad = ref(null);
const tema = ref("");
const origen = ref("");
const objetivo_general = ref("");
const objetivo_especifico = ref("");
const selected_competencia_corporativa = ref([]);
const selected_competencia_rol = ref([]);
const selected_competencia_posicion = ref([]);
const selected_macroproceso = ref([]);
const modalidad = ref(null);
const duracion_horas = ref(0);
const duracion_minutos = ref(0);
const metodologia = ref("");
const tipo = ref(null);
const proveedor = ref(null);
const macroproceso = ref(null);
const publico_objetivo = ref(null);
const ciudad = ref(null);
const evaluacion = ref("");
const seguimiento = ref("");
const fecha_inicio = ref(null);
const fecha_fin = ref(null);


const list_tipo_nivel_formacion = ref([]);
const list_tipo_actividad = ref([]);
const list_ciudades_formacion = ref([]);
const list_competencia_corporativa = ref([]);
const list_competencia_rol = ref([]);
const list_competencia_posicion = ref([]);
const list_macroprocesos = ref([]);
const list_tipo_modalidad = ref([]);

const proveedorBusqueda = ref("");
const proveedorId = ref("");
const proveedores = ref([]);
const mostrarLista = ref(false);

const modalInstance = ref(null);
const modalErrorInstance = ref(null);

const token_status = ref(0);

const msg = ref("");
const errorMsg = ref("");

const router = useRouter();

// ✅ Función que llama a la api para guardar la formación
const guardarFormacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        console.log(selected_competencia_corporativa.value);
        console.log(selected_competencia_rol.value);
        console.log(selected_competencia_posicion.value);
        console.log(selected_macroproceso.value);
        return ""
        const response = await axios.post(
            `${apiUrl}/guardar_formacion`, 
            {
                nivel_formacion: nivel_formacion.value,
                tipo_actividad: tipo_actividad.value,
                tema: tema.value.trim(),
                origen: origen.value.trim(),
                objetivo_general: objetivo_general.value.trim(),
                objetivo_especifico: objetivo_especifico.value.trim(),
                modalidad: modalidad.value,
                duracion_horas: duracion_horas.value,
                duracion_minutos: duracion_minutos.value,
                metodologia: metodologia.value.trim(),
                tipo: tipo.value,
                proveedor: proveedorId.value,
                ciudad: ciudad.value,
                evaluacion: evaluacion.value.trim(),
                seguimiento: seguimiento.value.trim(),
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
        if (response.status === 201) {
            msg.value = response.data.message;
            modalInstance.value.show();
        }

    } catch (error) {
        console.error('Error al guardar formación:', error);
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
            list_ciudades_formacion.value = response.data.data.ciudades_formacion;
            list_competencia_corporativa.value = response.data.data.competencia_corporativa;
            list_competencia_rol.value = response.data.data.competencia_rol;
            list_competencia_posicion.value = response.data.data.competencia_posicion;
            list_macroprocesos.value = response.data.data.macroprocesos;
            list_tipo_modalidad.value = response.data.data.tipo_modalidad;
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

// ✅ Watcher que esta pendiente si hay un cambio en el campo de busqueda
watch(proveedorBusqueda, async (nuevoValor) => {
    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }
    if (nuevoValor.length >= 2) { // Iniciar búsqueda después de 2 caracteres
        try {
            const response = await axios.post(`${apiUrl}/get_proveedores`, 
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
            proveedores.value = response.data.data; // Suponiendo que la API devuelve [{id: 1, nombre: "Proveedor 1"}, ...]
        } catch (error) {
            console.error("Error en la búsqueda:", error);
        }
    } else {
        proveedores.value = [];
    }
});

// ✅ Función que selecciona los datos del proveedor elegido en el input de proveedor
const seleccionarProveedor = (prov) => {
    proveedorBusqueda.value = prov.nombres + ' - ' + prov.nit;
    proveedorId.value = prov.id;
    mostrarLista.value = false;
};

// ✅ Función para ocultar la lista
const ocultarLista = () => {
    setTimeout(() => {
        mostrarLista.value = false;
    }, 200);
};

const toggleDropdown = (type) => {
    if (type == 'corp'){
        dropdownVisibleCorp.value = !dropdownVisibleCorp.value;
    }else if (type == 'rol'){
        dropdownVisibleRol.value = !dropdownVisibleRol.value;
    }else if (type == 'pos'){
        dropdownVisiblePos.value = !dropdownVisiblePos.value;
    }else if (type == 'macro'){
        dropdownVisibleMacro.value = !dropdownVisibleMacro.value;
    }
};

const toggleSelection = (id, type) => {
    if (type == 'corp'){
        const index = selected_competencia_corporativa.value.indexOf(id);
        if (index === -1) {
          selected_competencia_corporativa.value.push(id);
        } else {
          selected_competencia_corporativa.value.splice(index, 1);
        }
    } else if (type == 'rol'){
        const index2 = selected_competencia_rol.value.indexOf(id);
        if (index2 === -1) {
          selected_competencia_rol.value.push(id);
        } else {
          selected_competencia_rol.value.splice(index2, 1);
        }
    } else if (type == 'pos'){
        const index3 = selected_competencia_posicion.value.indexOf(id);
        if (index3 === -1) {
          selected_competencia_posicion.value.push(id);
        } else {
          selected_competencia_posicion.value.splice(index3, 1);
        }
    } else if (type == 'macro'){
        const index4 = selected_macroproceso.value.indexOf(id);
        if (index4 === -1) {
          selected_macroproceso.value.push(id);
        } else {
          selected_macroproceso.value.splice(index4, 1);
        }
    }
};

const getCompetenciaNombre = (id, type) => {
    if (type == 'corp'){
        const competencia = list_competencia_corporativa.value.find(c => c.id === id);
        return competencia ? competencia.nombre : "";
    }else if (type == 'rol'){
        const rol = list_competencia_rol.value.find(d => d.id === id);
        return rol ? rol.nombre : "";
    }else if (type == 'pos'){
        const posi = list_competencia_posicion.value.find(e => e.id === id);
        return posi ? posi.nombre : "";
    }else if (type == 'macro'){
        const macro = list_macroprocesos.value.find(m => m.id === id);
        return macro ? macro.nombre : "";
    } 
};

// Computed para verificar si todas están seleccionadas
const isAllSelected = computed(() => 
    selected_competencia_corporativa.value.length === list_competencia_corporativa.value.length
);

const isAllSelectedRol = computed(() => 
    selected_competencia_rol.value.length === list_competencia_rol.value.length
);

const isAllSelectedPos = computed(() => 
    selected_competencia_posicion.value.length === list_competencia_posicion.value.length
);

const isAllSelectedMacro = computed(() => 
    selected_macroproceso.value.length === list_macroprocesos.value.length
);

// Función para seleccionar/deseleccionar todas las opciones
const toggleAll = (type) => {
    if (type == 'corp'){
        if (isAllSelected.value) {
            selected_competencia_corporativa.value = []; // Si ya estaban todas, vaciar
        } else {
            selected_competencia_corporativa.value = list_competencia_corporativa.value.map(c => c.id); // Seleccionar todas
        }
    } else if (type == 'rol'){
        if (isAllSelectedRol.value) {
            selected_competencia_rol.value = []; // Si ya estaban todas, vaciar
        } else {
            selected_competencia_rol.value = list_competencia_rol.value.map(d => d.id); // Seleccionar todas
        }
    } else if (type == 'pos'){
        if (isAllSelectedPos.value) {
            selected_competencia_posicion.value = []; // Si ya estaban todas, vaciar
        } else {
            selected_competencia_posicion.value = list_competencia_posicion.value.map(e => e.id); // Seleccionar todas
        }
    } else if (type == 'macro'){
        if (isAllSelectedMacro.value) {
            selected_macroproceso.value = []; // Si ya estaban todas, vaciar
        } else {
            selected_macroproceso.value = list_macroprocesos.value.map(m => m.id); // Seleccionar todas
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
    nivel_formacion.value = null;
    tipo_actividad.value = null;
    tema.value = '';
    origen.value = '';
    objetivo_general.value = '';
    objetivo_especifico.value = '';
    selected_competencia_corporativa.value = null;
    selected_competencia_rol.value = null;
    selected_competencia_posicion.value = null;
    modalidad.value = null;
    duracion_horas.value = 0;
    duracion_minutos.value = 0;
    metodologia.value = '';
    tipo.value = null;
    proveedorBusqueda.value = '';
    proveedorId.value = '';
    macroproceso.value = null;
    publico_objetivo.value = null;
    ciudad.value = null;
    evaluacion.value = '';
    seguimiento.value = '';
    fecha_inicio.value = null;
    fecha_fin.value = null;
};

// Cierra todos los dropdowns al hacer clic fuera
const closeDropdowns = () => {
  dropdownVisibleCorp.value = false;
  dropdownVisibleRol.value = false;
  dropdownVisiblePos.value = false;
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
    gap: 16px; /* Espaciado entre elementos */
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

.input-field {
    width: 100%;
    padding: 8px;
    border: 1px solid #d1d5db;
    border-radius: 4px;
}

.submit-button {
    width: 100%;
    margin-top: 10px;
    padding: 10px;
    background: #6a5acd;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s;
    font-size: 0.9em;
}

.submit-button:hover {
    background: #5a4bbf;
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
  