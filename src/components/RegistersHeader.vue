<!-- Componente contendo botão de retorno e titulo dos componentes de Login, Register, Nurse -->
<template>
  <div class="mt-10 flex flex-col w-full">
    <router-link v-if="getData.backButton" to="/" class="self-start hover:opacity-70 cursor-pointer flex items-center ml-15 text-gray-400">
      <mdi:arrow-left class="mr-2" />
      Voltar ao inicio
    </router-link>
    <h1 class="text-gray-600 p-5  font-bold text-2em">
      {{ getData.Title }}
    </h1>
  </div>
</template>

<script>
export default {
  props: ['getData'],
}
</script>
