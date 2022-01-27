<template>
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

    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="password">
        Digite a senha
      </label>
      <input
        id="password"
        v-model="inputs.password"
        type="password"
        placeholder="Senha"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p v-if="empty.password" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira uma senha.
      </p>
    </div>
    <div>
      <label class="block text-gray-700 text-sm ml-1 font-bold mb-2" for="repassword">
        Confirme a senha
      </label>
      <input
        id="repassword"
        v-model="inputs.repassword"
        type="password"
        placeholder="Confirmar senha"
        class="appearance-none border rounded outline-none text-gray-500 leading-tight shadow w-full p-3"
      >
      <p v-if="empty.repassword" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor confirme sua senha.
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
import sha256 from 'crypto-js/sha256'

export default {
  directives: {
    maska,
  },
  data() {
    return {
      inputs: {
        name: '',
        cpf: '',
        password: '',
        repassword: '',
      },
      validCPF: true,
      errorMSG: '',
      empty: {
        name: false,
        cpf: false,
        password: false,
        repassword: false,
      },
    }
  },
  mounted() {
    const user = JSON.parse(localStorage.getItem('user-info'))
    if (!user) {
      this.$router.push('/')
    }
    else {
      if (user.function !== 'medico')
        this.$router.push('/')
    }
  },
  methods: {
    async handleSubmit() {
      this.errorMSG = ''
      const user = JSON.parse(localStorage.getItem('user-info'))
      if (!user || (user && user.function !== 'medico')) {
        this.$router.push('/')
        return
      }

      await this.emptyRules()
      for (const each in this.empty)
        if (this.empty[each]) return

      if (this.inputs.cpf.length < 14) {
        this.validCPF = false
        return
      }

      if (this.inputs.password !== this.inputs.repassword) {
        this.errorMSG = 'As senhas não conferem.'
        return
      }

      const cpf = sha256(this.inputs.cpf)
      const password = sha256(this.inputs.password + this.inputs.name)

      const data = {
        name: this.inputs.name,
        cpf: `${cpf}`,
        password: `${password}`,
        function: 'enfermeiro',
      }

      const res = await axios.get('staffs')
      if (res.data.some(e => e.cpf === `${cpf}`)) {
        this.errorMSG = 'Já existe um usuário cadastrado com esse CPF.'
        return
      }

      axios.post('staffs', data)
        .then((res) => {
          this.resetForm(true)
          this.$router.push('/done')
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
