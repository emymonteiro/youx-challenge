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
        v-model="inputs.name"
        type="text"
        placeholder="Nome"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p v-if="empty.name" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira seu nome.
      </p>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="cpf">
        Digite seu CPF
      </label>
      <input
        id="cpf"
        v-model="inputs.cpf"
        v-maska="'###.###.###-##'"
        type="text"
        placeholder="CPF"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p v-if="empty.cpf" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um CPF.
      </p>
      <p v-if="!validCPF" class="text-red-500 ml-1 mt-2 text-xs italic">
        Numero de CPF inválido.
      </p>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="password">
        Digite sua senha
      </label>
      <input
        id="password"
        v-model="inputs.password"
        type="password"
        placeholder="Senha"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p v-if="empty.password" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira sua senha.
      </p>
    </div>

    <p v-if="errorMSG !== ''" class="text-center text-red-500 ml-1 mt-2 font-bold">
      {{ errorMSG }}
    </p>

    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="handleSubmit">
      Logar
    </button>
  </form>
</template>

<script>

import { maska } from 'maska'
import axios from 'axios'

export default {
  directives: { maska },
  data() {
    return {
      inputs: {
        name: '',
        cpf: '',
        password: '',
      },
      validCPF: true,
      errorMSG: '',
      empty: {
        name: false,
        cpf: false,
        password: false,
      },
    }
  },
  mounted() {
    const user = localStorage.getItem('user-info')
    if (user)
      this.$router.push('/')
  },
  methods: {
    async handleSubmit() {
      await this.emptyRules()
      for (const each in this.empty)
        if (this.empty[each]) return

      if (this.inputs.cpf.length < 14) {
        this.validCPF = false
        return
      }

      const data = {}
      for (const each in this.inputs)
        data[each] = this.inputs[each]

      const res = await axios.get(`staffs?name=${this.inputs.name}&cpf=${this.inputs.cpf}&password=${this.inputs.password}`)
      if (!res.data.some(e => e.cpf === this.inputs.cpf)) {
        this.resetForm()
        this.errorMSG = 'Registro não encontrado.'
        return
      }

      if (res.status === 200 && res.data.length > 0) {
        this.resetForm(false, true)
        localStorage.setItem('user-info', JSON.stringify(res.data[0]))
        this.$router.push('/')
      }
    },
    emptyRules() {
      for (const each in this.empty)
        this.empty[each] = this.inputs[each] === ''
    },
    resetForm(forms, msg) {
      if (forms) {
        for (const each in this.inputs)
          this.inputs[each] = ''
      }

      this.errorMSG = msg ? '' : this.errorMSG
      this.validCPF = true
    },

  },
}
</script>
