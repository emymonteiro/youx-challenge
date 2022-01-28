<template>
  <!-- FORMULARIO DE CADASTRO DE ENFERMEIROS -->
  <form class="flex flex-col text-left w-[80%] pb-5 mt-5 space-y-5">
    <!-- INPUT NOME -->
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
    <!-- INPUT SENHA -->
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
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.password" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor insira uma senha.
      </p>
    </div>
    <!-- INPUT CONFIRME A SENHA -->
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
      <!-- VERIFICADOR DE CAMPO VAZIO -->
      <p v-if="empty.repassword" class="text-red-500 ml-1 mt-2 text-xs italic">
        Por favor confirme sua senha.
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
        password: '',
        repassword: '',
      },
      /* Handler para alterar validação do CPF */
      validCPF: true,
      /* Handler para mensagens de erro */
      errorMSG: '',
      /* Obj para loop de inputs vazios. */
      empty: {
        name: false,
        cpf: false,
        password: false,
        repassword: false,
      },
    }
  },
  mounted() {
    /* Ao montar, verifica se o usuário está logado, e também se possui o cargo médico, caso contrário redireciona pra index */
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
    /* handler pro registro de enfermeiros */
    async handleSubmit() {
      /* Reseta mensagens de erros */
      this.errorMSG = ''
      /* Verifica se o usuário está logado e é médico, caso contrario, retorna ao index */
      const user = JSON.parse(localStorage.getItem('user-info'))
      if (!user || (user && user.function !== 'medico')) {
        this.$router.push('/')
        return
      }

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

      /* Validação de Match das senhas */
      if (this.inputs.password !== this.inputs.repassword) {
        this.errorMSG = 'As senhas não conferem.'
        return
      }

      /* Encriptação de CPF e SENHA */
      const cpf = sha256(this.inputs.cpf)
      const password = sha256(this.inputs.password + this.inputs.name)

      /* Mapeia a data do novo usuário */
      const data = {
        name: this.inputs.name,
        cpf: `${cpf}`,
        password: `${password}`,
        function: 'enfermeiro',
      }

      /* Verifica se o CPF já foi utilizado */
      const res = await axios.get('staffs')
      if (res.data.some(e => e.cpf === `${cpf}`)) {
        this.errorMSG = 'Já existe um usuário cadastrado com esse CPF.'
        return
      }

      /* Cria um novo usuário recebendo possiveis erros de API */
      axios.post('staffs', data)
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
