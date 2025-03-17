<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>

        <div class="container">
            <div class="header">
                <h1>Registro de Formaciones</h1>
                <input type="text" placeholder="Buscar formación..." class="search-input">
            </div>
            <div class="form-container">
                <h2>Registrar Nueva Formación</h2>
                <form class="form-flex">
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
                        <input type="date" class="input-field" v-model="fecha_fin">
                    </div>
                    <button type="submit" class="submit-button" @click="enviar">Registrar Formación</button>
                </form>
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

const token = ref("");
const nombre = ref("");
const cedula = ref("");

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

const msg = ref("");
const errorMsg = ref("");

const router = useRouter();

const enviar = async () => {
    console.log(proveedorBusqueda.value);
    console.log(proveedorId.value);
}


// ✅ Función para cargar los select generales del formulario
const cargarDatos = async () => {
    try {
        const response = await axios.post(
            `${apiUrl}/get_parametros`, {},
            {
                headers: {
                    Accept: "application/json",
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
        errorMsg.value = error.response.data.message;
    }
};
watch(proveedorBusqueda, async (nuevoValor) => {
    if (nuevoValor.length >= 2) { // Iniciar búsqueda después de 2 caracteres
        try {
            const response = await axios.post(`${apiUrl}/get_proveedores`, 
                {
                    valor: nuevoValor
                },
                {
                    headers: {
                        Accept: "application/json",
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

const seleccionarProveedor = (prov) => {
    proveedorBusqueda.value = prov.nombres + ' - ' + prov.nit;
    proveedorId.value = prov.id;
    mostrarLista.value = false;
};

const ocultarLista = () => {
    setTimeout(() => {
        mostrarLista.value = false;
    }, 200);
};

onMounted(() => {
    token.value = localStorage.getItem("token");
    nombre.value = localStorage.getItem("nombre");
    cedula.value = localStorage.getItem("cedula");

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
  