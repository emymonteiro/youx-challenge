<!-- Componente de botões para redirecionamento para registro de pacientes ou enfermeiros -->
<template>
  <router-link
    to="/register"
    class="hover:opacity-70 font-bold px-5 py-1 rounded-lg"
  >
    Registrar Paciente
  </router-link>
  <router-link
    v-if="isDoctor"
    to="/nurse"
    class="hover:opacity-70 font-bold px-5 py-1 rounded-lg"
  >
    Cadastrar Enfermeiro
  </router-link>
</template>

<script>
export default {
  props: ['isDoctor'],
}
</script>
