<template>
  <h1 class="text-gray-400 font-bold p-5 text-2em">
    Login
  </h1>
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="username">
        Digite seu Nome
      </label>
      <input
        id="username"
        type="text"
        placeholder="Nome"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira seu nome.
      </p>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="cpf">
        Digite seu CPF
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
    <div>
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
    </div>
    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button">
      Logar
    </button>
  </form>
</template>

<script lang="ts">

import { maska } from 'maska'

export default {
  directives: { maska },
}
</script>
