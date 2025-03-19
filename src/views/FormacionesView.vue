<template>
    <LayoutView>
        <h3> Bienvenido, {{ nombre }}</h3>

        <div class="container">
            <div class="header">
                <h1>Busqueda de Formaciones</h1>
                <input type="text" placeholder="Buscar formación..." class="search-input" @input="filtrarFormaciones" v-model="busqueda">
            </div>
            <div class="form-container">
                <div class="card-container">
                    <div v-for="formacion in list_formaciones" :key="formacion.id" class="card" @click="editarFormacion(formacion.id)">
                        <h5>{{ formacion.codigo }} - {{ formacion.tema }}</h5>
                        <p><b>Modalidad: </b>{{ formacion.modalidad }}</p>
                        <p><b>Fecha Inicio: </b>{{ formacion.fecha_inicio }}</p>
                        <p><b>Fecha Fin: </b>{{ formacion.fecha_fin }}</p>
                        <p><b>Estado: </b>{{ formacion.estado_formacion }}</p>
                        <!-- Aquí puedes agregar más detalles de la formación -->
                    </div>
                </div>
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
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import { Modal } from 'bootstrap';

const token = ref("");
const nombre = ref("");
const cedula = ref("");

const list_formaciones = ref([]);
const busqueda = ref("");

const modalInstance = ref(null);
const modalErrorInstance = ref(null);

const token_status = ref(0);

const msg = ref("");
const errorMsg = ref("");

const router = useRouter();

// ✅ Función para filtrar las formaciones
const filtrarFormaciones = async () => {
    if (!token.value) {
        router.push('/'); // Redirigir al login si no hay token
    }
    if (busqueda.value.length >= 2) { // Iniciar búsqueda después de 2 caracteres
        try {
            const response = await axios.post(`${apiUrl}/get_formaciones`, 
                {
                    valor: busqueda.value
                },
                {
                    headers: {
                        Accept: "application/json",
                        Authorization: `Bearer ${token.value}`
                    }
                }
            );
            list_formaciones.value = response.data.data;
        } catch (error) {
            console.error("Error en la búsqueda:", error);
        }
    } else {
        list_formaciones.value = [];
    }
};

const editarFormacion = (id) => {
    router.push({ name: 'editar-formacion', params: { id } });
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

.card-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 20px;
    margin-top: 20px;
}

.card {
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s;
}

.card:hover {
    transform: scale(1.05);
    cursor: pointer;
}
  
</style>
  