<template>
  <h1 class="text-gray-400 font-bold p-5 text-2em">
    Registro
  </h1>
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
    <div class="flex w-full space-x-10">
      <button class="w-full bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button">
        Paciente
      </button>
      <button class="w-full bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button">
        Enfermeiro
      </button>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="username">
        Digite o Nome
      </label>
      <input
        id="username"
        type="text"
        placeholder="Nome"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um nome.
      </p>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="cpf">
        Digite o CPF
      </label>
      <input
        id="cpf"
        v-maska="'###.###.###-##'"
        type="text"
        placeholder="CPF"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um CPF.
      </p>
    </div>
    <!-- Senha para o Enfermeiro -->
    <!-- <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="password">
        Digite sua senha
      </label>
      <input
        id="password"
        type="text"
        placeholder="Senha"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira sua senha.
      </p>
    </div> -->
    <!-- Campos para o Paciente -->
    <div class="flex space-x-5 w-full overflow-hidden">
      <div class="w-[60%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="date">
          Data de Nascimento
        </label>
        <input id="date" class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3" type="date">
      </div>
      <div class="w-[30%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="weight">
          Peso (KG)
        </label>
        <input
          id="weight"
          v-maska="['#','##','###']"
          class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
          type="text"
        >
      </div>
      <div class="w-[30%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="height">
          Altura
        </label>
        <input
          id="height"
          v-maska="'#.##'"
          class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
          type="text"
        >
      </div>
    </div>
    <label class="block text-gray-700 text-sm ml-1 font-bold mb-2">
      Estado
    </label>
    <select class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3">
      <option>Estado</option>
    </select>
    <!--  -->
    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button">
      Registrar
    </button>
  </form>
</template>

<script lang="ts">

import { maska } from 'maska'

export default {
  directives: { maska },
}
</script>
