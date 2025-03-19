<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>

        <div class="container">
            <div class="form-container">
                <h2>Editar Formación</h2>
                <form @submit.prevent="" class="form-flex">
                    <div class="form-group">
                        <label>Nivel de Formación:</label>
                        <input type="text" class="input-field" v-model="nivel_formacion" readonly>
                    </div>
                    <div class="form-group">
                        <label>Tipo de Actividad:</label>
                        <input type="text" class="input-field" v-model="tipo_actividad" readonly>
                    </div>
                    <div class="form-group">
                        <label>Tema:</label>
                        <textarea
                            v-model="tema"
                            class="input-field"
                            readonly
                        ></textarea>
                    </div>
                    <div class="form-group">
                        <label>Origen de la Necesidad:</label>
                        <textarea
                            v-model="origen"
                            class="input-field"
                            readonly
                        ></textarea>
                    </div>
                    <div class="form-group">
                        <label>Objetivo General:</label>
                        <textarea
                            v-model="objetivo_general"
                            class="input-field"
                            readonly
                        ></textarea>
                    </div>
                    <div class="form-group">
                        <label>Objetivos Específicos:</label>
                        <textarea
                            v-model="objetivo_especifico"
                            class="input-field"
                            readonly
                        ></textarea>
                    </div>
                    <div class="form-group">
                        <label>Competencias Corporativas:</label>
                        <select class="input-field" v-model="competencia_corporativa">
                            <option :value="null">Seleccione...</option>
                            <option v-for="compe_corp in list_competencia_corporativa" :value="compe_corp.id">{{ compe_corp.orden }}. {{ compe_corp.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Competencias de Rol:</label>
                        <select class="input-field" v-model="competencia_rol">
                            <option :value="null">Seleccione...</option>
                            <option v-for="compe_rol in list_competencia_rol" :value="compe_rol.id">{{ compe_rol.orden }}. {{ compe_rol.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Competencias de Posición:</label>
                        <select class="input-field" v-model="competencia_posicion">
                            <option :value="null">Seleccione...</option>
                            <option v-for="compe_pos in list_competencia_posicion" :value="compe_pos.id">{{ compe_pos.orden }}. {{ compe_pos.nombre }}</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Modalidad:</label>
                        <input type="text" class="input-field" v-model="modalidad" readonly>
                    </div>
                    <div class="form-group">
                        <label>Duración Horas:</label>
                        <input type="number" class="input-field" v-model="duracion_horas" readonly>
                    </div>
                    <div class="form-group">
                        <label>Duración Minutos:</label>
                        <input type="number" class="input-field" v-model="duracion_minutos" readonly>
                    </div>
                    <div class="form-group">
                        <label>Metodología y Contenido:</label>
                        <input type="text" class="input-field" v-model="metodologia" readonly>
                    </div>
                    <div class="form-group">
                        <label>Tipo:</label>
                        <input type="text" class="input-field" v-model="tipo" readonly>
                    </div>
                    <div class="form-group">
                        <label>Proveedor:</label>
                        <input type="text" class="input-field" v-model="proveedorBusqueda" readonly>
                    </div>
                    <div class="form-group">
                        <label>Macroproceso:</label>
                        <select class="input-field" v-model="macroproceso">
                            <option :value="null">Seleccione...</option>
                            <option v-for="tip_macro in list_macroprocesos" :value="tip_macro.id">{{ tip_macro.nombre }}</option>
                        </select>
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
                        <textarea
                            v-model="evaluacion"
                            class="input-field"
                            required
                        ></textarea>
                    </div>
                    <div class="form-group">
                        <label>Seguimiento y Retroalimentación:</label>
                        <textarea
                            v-model="seguimiento"
                            class="input-field"
                            required
                        ></textarea>
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
import { ref, onMounted, watch } from "vue";
import { useRouter, useRoute } from "vue-router";
import axios from "axios";
import { Modal } from 'bootstrap';

const token = ref("");
const nombre = ref("");
const cedula = ref("");

const formacion_id = ref("");
const data_formacion = ref(null);

const nivel_formacion = ref(null);
const tipo_actividad = ref(null);
const tema = ref("");
const origen = ref("");
const objetivo_general = ref("");
const objetivo_especifico = ref("");
const competencia_corporativa = ref(null);
const competencia_rol = ref(null);
const competencia_posicion = ref(null);
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
const route = useRoute();

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
        console.log(response);
        if (response.status === 200) {
            nivel_formacion.value = response.data.data.nivel_formacion_nombre;
            tipo_actividad.value = response.data.data.tipo_actividad_nombre;
            tema.value = response.data.data.tema;
            origen.value = response.data.data.origen;
            objetivo_general.value = response.data.data.objetivo_general;
            objetivo_especifico.value = response.data.data.objetivo_especifico;
            modalidad.value = response.data.data.modalidad_nombre;
            duracion_horas.value = response.data.data.duracion_horas;
            duracion_minutos.value = response.data.data.duracion_minutos;
            metodologia.value = response.data.data.metodologia;
            tipo.value = response.data.data.tipo_nombre;
            proveedorBusqueda.value = response.data.data.proveedor_nombre;
            ciudad.value = response.data.data.ciudad;
            evaluacion.value = response.data.data.evaluacion;
            seguimiento.value = response.data.data.seguimiento;
            fecha_inicio.value = response.data.data.fecha_inicio;
            fecha_fin.value = response.data.data.fecha_fin;
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
const guardarFormacion = async () => {
    try {
        if (!token.value) {
            router.push('/'); // Redirigir al login si no hay token
        }
        const response = await axios.post(
            `${apiUrl}/guardar_formacion`, 
            {
                nivel_formacion: nivel_formacion.value,
                tipo_actividad: tipo_actividad.value,
                tema: tema.value,
                origen: origen.value,
                objetivo_general: objetivo_general.value,
                objetivo_especifico: objetivo_especifico.value,
                modalidad: modalidad.value,
                duracion_horas: duracion_horas.value,
                duracion_minutos: duracion_minutos.value,
                metodologia: metodologia.value,
                tipo: tipo.value,
                proveedor: proveedorId.value,
                ciudad: ciudad.value,
                evaluacion: evaluacion.value,
                seguimiento: seguimiento.value,
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
    competencia_corporativa.value = null;
    competencia_rol.value = null;
    competencia_posicion.value = null;
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

// ✅ Función mounted que carga información ANTES de que la página renderice
onMounted(() => {
    token.value = localStorage.getItem("token");
    nombre.value = localStorage.getItem("nombre");
    cedula.value = localStorage.getItem("cedula");

    formacion_id.value = route.params.id; // Capturar el ID de la URL

    modalInstance.value = new Modal(exitoModal);
    modalErrorInstance.value = new Modal(errorModal);

    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }

    cargarDatos();
    getFormacion();
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
  