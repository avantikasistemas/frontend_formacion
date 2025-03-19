<template>
    <LayoutView>
        <h3> Bienvenido</h3>

        <div class="container">

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

            <!-- Segundo Select: Opciones Relacionadas -->
            <div class="form-group">
                <label>Opciones Relacionadas:</label>
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
                        <div v-for="(grupo, key) in list_opciones" :key="key">
                            <div class="dropdown-header">{{ key }}</div>
                            <div v-for="opcion in grupo" :key="opcion.id" class="dropdown-item" :class="{ 'selected': selected_opciones.includes(opcion.id) }" @click.stop="toggleSelection(opcion.id, 'opcion')">
                                {{ opcion.nombre }} <span v-if="selected_opciones.includes(opcion.id)">✓</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </LayoutView>
  </template>
  
<script setup>
import apiUrl from "../../config.js";
import LayoutView from '../views/Layouts/LayoutView.vue';
import { ref, onMounted, computed, watch } from "vue";
import axios from "axios";
const token = ref("");

const list_macroprocesos = ref([]);
const dropdownVisibleMacro = ref(false);
const dropdownVisibleOpciones = ref(false);
const selected_macroproceso = ref([]);
const selected_opciones = ref([]);
const list_opciones = ref({});

const toggleDropdown = (type) => {
    if (type == 'macro') dropdownVisibleMacro.value = !dropdownVisibleMacro.value;
    if (type == 'opciones') dropdownVisibleOpciones.value = !dropdownVisibleOpciones.value;
};

const toggleSelection = (id, type) => {
    if (type == 'macro') {
        const index = selected_macroproceso.value.indexOf(id);
        if (index === -1) selected_macroproceso.value.push(id);
        else selected_macroproceso.value.splice(index, 1);
    } else if (type == 'opcion') {
        const index = selected_opciones.value.indexOf(id);
        if (index === -1) selected_opciones.value.push(id);
        else selected_opciones.value.splice(index, 1);
    }
};

const getCompetenciaNombre = (id, type) => {
    if (type == 'macro') {
        const macro = list_macroprocesos.value.find(m => m.id === id);
        return macro ? macro.nombre : "";
    } else if (type == 'opcion') {
        for (const key in list_opciones.value) {
            const opcion = list_opciones.value[key].find(o => o.id === id);
            if (opcion) return opcion.nombre;
        }
        return "";
    }
};

const isAllSelectedMacro = computed(() => selected_macroproceso.value.length === list_macroprocesos.value.length);
const isAllSelectedOpciones = computed(() => {
    const allOpciones = Object.values(list_opciones.value).flat().map(o => o.id);
    return selected_opciones.value.length === allOpciones.length;
});

// Función para seleccionar/deseleccionar todas las opciones
const toggleAll = (type) => {
    if (type == 'macro') {
        selected_macroproceso.value = isAllSelectedMacro.value ? [] : list_macroprocesos.value.map(m => m.id);
    } else if (type == 'opciones') {
        const allOpciones = Object.values(list_opciones.value).flat().map(o => o.id);
        selected_opciones.value = isAllSelectedOpciones.value ? [] : allOpciones;
    }
};

// ✅ Función para cargar los select generales del formulario
const cargarDatos = async () => {
    try {
        // if (!token.value) {
        //     router.push('/'); // Redirigir al login si no hay token
        // }
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
            list_macroprocesos.value = response.data.data.macroprocesos;
        }

    } catch (error) {
        console.error('Error al cargar los datos:', error);
    }
};

const cargarOpcionesRelacionadas = async () => {
    try {
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
        if (response.status === 200) list_opciones.value = response.data.data.opciones;
    } catch (error) {
        console.error('Error al cargar las opciones:', error);
    }
};

watch(selected_macroproceso, cargarOpcionesRelacionadas);

// ✅ Función mounted que carga información ANTES de que la página renderice
onMounted(() => {
    token.value = localStorage.getItem("token");
    // if (!token.value) {
    //     router.push('/'); // Redirigir al login si no hay token
    // }

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

</style>
  