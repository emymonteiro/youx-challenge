<!-- Pagina Inicial , Redireciona para o Login caso o usuário esteja desconectado -->

<template>
  <div class="absolute bg-white shadow flex justify-between w-[60%] rounded text-gray-600">
    <div class="flex py-10 flex-col items-center justify-center w-full ">
      <Map @update-map="updateMap" />
      <button class="justify-self-start hover:opacity-70 text-white self-start mb-5 ml-5 bg-green-600 py-1 rounded px-5" @click="resetMap()">
        Ver Todos
      </button>
      <div class="w-full text-left pt-5 border-t">
        <div v-if="patients" class="ml-5">
          <p>Estado: <span class="font-bold">{{ selected.sigla }}</span></p>
          <p>Pacientes: <span class="font-bold">{{ selected.amount }}</span></p>
        </div>
      </div>
    </div>
    <div class="flex flex-col pt-10 space-y-3 shadow items-center justify-start w-100 ">
      <RegisterButtons :is-doctor="isDoctor" />
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { maska } from 'maska'
import RegisterButtons from '~/components/RegisterButtons.vue'

export default {
  directives: { maska },
  components: { RegisterButtons },
  data() {
    return {
      selected: {
        sigla: 'Todos',
        amount: 0,
        patients: {}, // Futura implementação da listagem dos pacientes através da API
      },
      patients: null,
      isDoctor: false,
    }
  },
  async mounted() {
    const user = JSON.parse(localStorage.getItem('user-info')) // Getter localStorage do Login de usuário
    if (!user) {
      this.$router.push('/login')
      return
    }
    if (user && user.function === 'medico') // Getter verificando cargo
      this.isDoctor = true
    const res = await axios.get('patients') // Listagem de Pacientes
    this.patients = res.data
    this.selected.amount = this.patients.length
  },
  methods: {
    async updateMap(e) { // Evento handler para o Emit do clique nos estados do mapa do Brasil
      const res = await axios.get(`patients?state=${e}`)
      this.selected.sigla = e
      this.selected.amount = res.data.length
    },
    async resetMap() { // Reseta o mapa para default ( nenhum estado selecionado )
      const res = await axios.get('patients')
      this.patients = res.data
      this.selected.sigla = 'Todos'
      this.selected.amount = this.patients.length
    },
  },
}
</script>
