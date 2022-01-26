<template>
  <h1 class="text-gray-400 font-bold pt-10 p-5 text-2em">
    Registrar Paciente
  </h1>
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
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
      <p v-if="empty.name" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um nome.
      </p>
    </div>
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
      <p v-if="empty.cpf" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira um CPF.
      </p>
      <p v-if="!validCPF" class="text-red-500 ml-1 mt-2 text-xs italic">
        Numero de CPF inválido.
      </p>
    </div>

    <!-- Campos para o Paciente -->
    <div class="flex space-x-5 w-full overflow-hidden">
      <div class="w-[60%]">
        <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="date">
          Data de Nascimento
        </label>
        <input id="date" v-model="inputs.date" class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3" type="date">
      </div>
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
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2">
        Estado
      </label>
      <select v-model="inputs.state" class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3">
        <option v-for="item in store" :key="item.id">
          {{ item.sigla }}
        </option>
      </select>
      <p v-if="empty.state" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor selecione um estado.
      </p>
    </div>

    <p v-if="errorMSG !== ''" class="text-center text-red-500 ml-1 mt-2 font-bold">
      {{ errorMSG }}
    </p>

    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold p-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="handleSubmit">
      Registrar
    </button>
  </form>
</template>

<script>
import { maska } from 'maska'
import axios from 'axios'

export default {
  directives: {
    maska,
  },
  data() {
    return {
      inputs: {
        name: '',
        cpf: '',
        date: '',
        weight: '',
        height: '',
        state: '',
      },
      validCPF: true,
      errorMSG: '',
      empty: {
        name: false,
        cpf: false,
        state: false,
      },
      store: null,
    }
  },
  async created() {
  // GET request using fetch with async/await
    const response = await fetch('https://servicodados.ibge.gov.br/api/v1/localidades/estados')
    const data = await response.json()
    this.store = data
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

      const res = await axios.get('patients')
      if (res.data.some(e => e.cpf === this.inputs.cpf)) {
        this.errorMSG = 'Já existe um usuário cadastrado com esse CPF.'
        this.resetForm()
        return
      }

      axios.post('patients', data)
        .then((res) => {
          this.resetForm(true)
          this.$router.push('/register')
        }).catch(() => {
          this.errorMSG = 'Ocorreu algum erro inexperado.'
          this.resetForm()
        })
    },
    emptyRules() {
      for (const each in this.empty)
        this.empty[each] = this.inputs[each] === ''
    },

    resetForm(arg) {
      for (const each in this.inputs)
        this.inputs[each] = ''

      this.errorMSG = arg ? '' : this.errorMSG
      this.validCPF = true
    },
  },
}
</script>
