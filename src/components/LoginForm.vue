<template>
  <!-- FORMULARIO DE LOGIN -->
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
    <!-- INPUT NOME -->
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
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.name" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira seu nome.
      </p>
    </div>
    <!-- INPUT CPF -->
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
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.cpf" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um CPF.
      </p>
      <!-- VERIFICADOR DE CPF VALIDO -->
      <p v-if="!validCPF" class="text-red-500 ml-1 mt-2 text-xs italic">
        Numero de CPF inválido.
      </p>
    </div>
    <!-- INPUT SENHA -->
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
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.password" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira sua senha.
      </p>
    </div>
    <!-- HANDLER PARA MENSAGENS DE ERRO  -->
    <p v-if="errorMSG !== ''" class="text-center text-red-500 ml-1 mt-2 font-bold">
      {{ errorMSG }}
    </p>
    <!-- BOTAO DE LOGAR -->
    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="handleSubmit">
      Logar
    </button>
  </form>
</template>

<script>
import { maska } from 'maska'
import axios, { Axios } from 'axios'
import sha256 from 'crypto-js/sha256'

export default {
  directives: { maska },
  data() {
    return {
      inputs: { /* DATA COM VALOR DE TODOS OS INPUTS */
        name: '',
        cpf: '',
        password: '',
      },
      validCPF: true, /* Handler para alterar valideção do CPF */
      errorMSG: '', /* Handler para mensagens de erro */
      empty: { /* Obj para loop de inputs vazios. */
        name: false,
        cpf: false,
        password: false,
      },
    }
  },
  mounted() { /* Ao finalizar o mount, verifica se o usuario já está logado e redireciona para a pagina inicial */
    const user = localStorage.getItem('user-info')
    if (user)
      this.$router.push('/')
  },
  methods: {
    /* Função do botão de registro */
    async handleSubmit() {
      /* Ativa todas as mensangens para inputs vazios */
      await this.emptyRules()
      /* Verifica inputs vazios e se existir, sai da function */
      for (const each in this.empty)
        if (this.empty[each]) return

      /* Validação para CPF */
      if (this.inputs.cpf.length < 14) {
        this.validCPF = false
        return
      }

      /* Cria um novo obj com os valores dos inputs */
      const data = {}
      for (const each in this.inputs)
        data[each] = this.inputs[each]

      /* Criptografia de CPF e SENHA */
      const cpf = sha256(this.inputs.cpf)
      const password = sha256(this.inputs.password + this.inputs.name)

      /* Filtragem de usuário no banco de dados json-server */
      const res = await axios.get(`staffs?name=${this.inputs.name}&cpf=${cpf}&password=${password}`)
      if (!res.data.some(e => e.cpf === `${cpf}`)) {
        this.resetForm()
        this.errorMSG = 'Registro não encontrado.'
        return
      }

      /* Verifica o status e tamanho do objeto, seta o usuário como logado, redireciona ao index */
      if (res.status === 200 && res.data.length > 0) {
        this.resetForm(false, true)
        localStorage.setItem('user-info', JSON.stringify(res.data[0]))
        this.$router.push('/')
      }
    },
    /* Função para ativar mensagens de required para todos os campos vazios ( que são requiridos ) */
    emptyRules() {
      for (const each in this.empty)
        this.empty[each] = this.inputs[each] === ''
    },
    /* Função para resetar o formulario, assim com suas mensagens de erro */
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
