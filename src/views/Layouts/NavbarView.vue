<template>
    <div class="main-navbar">
      <aside class="sidebar">
        <h2>Formaciones</h2>
        <nav>
          <ul>
            <li><a href="#" class="active"><i class="fa-solid fa-file"></i> Registro</a></li>
            <li><a href="#"><i class="fa-regular fa-folder-open"></i> Mis Formaciones</a></li>
            <li><a href="#"><i class="fa-solid fa-user"></i> Perfil</a></li>
            <li><a href="#"><i class="fa-solid fa-gear"></i> Configuraciones</a></li>
          </ul>
        </nav>
        <button class="logout" @click="logout">Cerrar Sesión</button>
      </aside>
    </div>
  </template>
  
<script setup>

  import { ref, onMounted } from "vue";
  import { useRouter } from "vue-router";
  
  const token = ref("");
  const nombre = ref("");
  const cedula = ref("");
  
  const router = useRouter();
  
  const logout = () => {
      localStorage.clear();
      router.push('/'); // Redirigir al login
  }
  
  
  onMounted(() => {
      token.value = localStorage.getItem("token");
      nombre.value = localStorage.getItem("nombre");
      cedula.value = localStorage.getItem("cedula");
  });
</script>
  
<style scoped>
  body {
    font-family: 'Arial', sans-serif;
    display: flex;
    margin: 0;
    padding: 0;
    background: #f5f5fa;
    width: 250px;
    height: 100vh;
    overflow: hidden;
  }
  
  .main-navbar {
    display: flex;
    height: 100vh;
  }
  
  .sidebar {
    width: 250px;
    height: 100vh;
    background: #2c2e48;
    color: white;
    padding: 20px;
    display: flex;
    flex-direction: column;
    position: fixed;
    left: 0;
    top: 0;
    bottom: 0;
  }
  
  .sidebar h2 {
    text-align: center;
    font-weight: bold;
    font-size: 1.6rem;
  }
  
  nav ul {
    list-style: none;
    padding: 0;
  }

  nav {
    flex-grow: 1; /* Empuja el botón hacia abajo */
  }
  
  nav ul li {
    margin: 20px 0;
  }
  
  nav ul li a {
    color: white;
    text-decoration: none;
    padding: 10px;
    display: block;
    border-radius: 5px;
    transition: background 0.3s;
  }
  
  nav ul li a:hover,
  .active {
    background: #454866;
  }
  
  .logout {
    background: #e74c3c;
    color: white;
    border: none;
    padding: 10px;
    cursor: pointer;
    border-radius: 5px;
    text-align: center;
  }
  
  
</style>
  