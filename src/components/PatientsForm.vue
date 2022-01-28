<template>
  <!-- FORMULARIO DE CADASTRO DE PACIENTES -->
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
    <!-- INPUT NAME -->
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="username">
        Digite o Nome
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
        Por favor insira um nome.
      </p>
    </div>
    <!-- INPUT CPF -->
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="cpf">
        Digite o CPF
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
    <!-- NOVOS INPUTS  -->
    <div class="flex space-x-5 w-full overflow-hidden">
      <!-- INPUT DATA DE NASCIMENTO -->
      <div class="w-[60%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="date">
          Data de Nascimento
        </label>
        <input id="date" v-model="inputs.date" class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3" type="date">
      </div>
      <!-- INPUT PESO -->
      <div class="w-[30%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="weight">
          Peso (KG)
        </label>
        <input
          id="weight"
          v-model="inputs.weight"
          v-maska="['#','##','###']"
          class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
          type="text"
        >
      </div>
      <!-- INPUT ALTURA -->
      <div class="w-[30%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="height">
          Altura
        </label>
        <input
          id="height"
          v-model="inputs.height"
          v-maska="'#.##'"
          class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
          type="text"
        >
      </div>
    </div>
    <!-- SELECT PARA OS ESTADOS -->
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2">
        Estado
      </label>
      <select v-model="inputs.state" class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3">
        <option v-for="item in store" :key="item.id">
          {{ item.sigla }}
        </option>
      </select>
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.state" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor selecione um estado.
      </p>
    </div>
    <!-- HANDLER PARA MENSAGENS DE ERRO  -->
    <p v-if="errorMSG !== ''" class="text-center text-red-500 ml-1 mt-2 font-bold">
      {{ errorMSG }}
    </p>
    <!-- BOTAO DE REGISTRO -->
    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="handleSubmit">
      Registrar
    </button>
  </form>
</template>

<script>
import { maska } from 'maska'
import axios from 'axios'
import sha256 from 'crypto-js/sha256'

export default {
  directives: {
    maska,
  },
  data() {
    return {
      /* DATA COM VALOR DE TODOS OS INPUTS */
      inputs: {
        name: '',
        cpf: '',
        date: '',
        weight: '',
        height: '',
        state: '',
      },
      /* Handler para alterar validação do CPF */
      validCPF: true,
      /* Handler para mensagens de erro */
      errorMSG: '',
      /* Obj para loop de inputs vazios. */
      empty: {
        name: false,
        cpf: false,
        state: false,
      },
      /* Armazenador para API dos estados */
      store: null,
    }
  },
  async created() {
    /* Ao ser criado, ativa a API contendo os estados do Brasil e armazena em store */
    const response = await fetch('https://servicodados.ibge.gov.br/api/v1/localidades/estados')
    const data = await response.json()
    this.store = data
  },
  mounted() {
    /* Ao montar verifica se o usuário está conectado e se não estiver redireciona para o index */
    const user = localStorage.getItem('user-info')
    if (!user)
      this.$router.push('/')
  },
  methods: {
    /* Botao de confirmar criação de novo paciente */
    async handleSubmit() {
      /* Verifica se o user está logado e redireciona ao index em caso contrário */
      const user = localStorage.getItem('user-info')
      if (!user)
        this.$router.push('/')

      /* Ativa todas as mensangens para inputs vazios */
      await this.emptyRules()

      /* Verifica inputs vazios e se existir, sai da function */
      for (const each in this.empty) {
        if (this.empty[each])
          return
      }

      /* Validação para CPF */
      if (this.inputs.cpf.length < 14) {
        this.validCPF = false
        return
      }

      /* Cria um novo obj com os valores dos inputs */
      const data = {}
      for (const each in this.inputs)
        data[each] = this.inputs[each]

      /* Criptografa o CPF */
      data.cpf = `${sha256(data.cpf)}`

      /* Verifica se o ja existe algum user com esse CPF */
      const res = await axios.get('patients')
      if (res.data.some(e => e.cpf === data.cpf)) {
        this.errorMSG = 'Já existe um usuário cadastrado com esse CPF.'
        this.resetForm()
        return
      }
      /* Envia as informaçoes de cadastro pro json-server */
      axios.post('patients', data)
        .then((res) => {
          this.resetForm(true)
          this.$router.push('/done')
        }).catch(() => {
          this.errorMSG = 'Ocorreu algum erro inexperado.'
          this.resetForm()
        })
    },
    /* Função para ativar mensagens de required para todos os campos vazios ( que são requiridos ) */
    emptyRules() {
      for (const each in this.empty)
        this.empty[each] = this.inputs[each] === ''
    },
    /* Função para resetar o formulario, assim com suas mensagens de erro */
    resetForm(arg) {
      for (const each in this.inputs)
        this.inputs[each] = ''
      this.errorMSG = arg ? '' : this.errorMSG
      this.validCPF = true
    },
  },
}
</script>
