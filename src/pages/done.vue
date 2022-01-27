<template>
  <div class="flex space-y-10 flex-col items-center justify-center font-bold uppercase p-10 text-gray-600">
    <mdi:check class="w-15 h-15 text-green-700" />
    <h1 class="text-1.5em">
      Cadastro realizado com sucesso
    </h1>
    <button class="bg-gray-700 text-white px-5 py-1 rounded hover:opacity-70" @click="$router.push('/')">
      Voltar
    </button>
  </div>
</template>
