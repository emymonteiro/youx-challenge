<template>
  <!-- HEADER BAR -->
  <main class="flex bg-white items-center shadow p-6 text-youx justify-between">
    <div class="cursor-pointer ml-[20%] flex flex-col" @click="goHome()">
      <!-- LOGO YOUX -->
      <div class="tipFont relative flex items-center whitespace-nowrap">
        <h1 class=" whitespace-nowrap text-2.4em font-bold">
          YOU
        </h1>
        <svg class="fill-youx h-[1.90rem] w-[1.90rem]" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="200" height="200" viewBox="0 0 200 200">
          <image id="Camada_0" data-name="Camada 0" x="7" y="6" width="187" height="188" xlink:href="data:img/png;base64,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" />
          <path id="Preenchimento_de_Cor_1" data-name="Preenchimento de Cor 1" class="cls-1" d="M7,7H194V193H7V7ZM34,33V60H61V87H87v26H61v27H34v27l27-1V140H87l1-27h26v27h27v27h26V140H141q-0.5-13.5-1-27H114V87l27-1V60h26V33H141V60H114V87H87V60H61V33H34Z" />
        </svg>
      </div>
      <div class="pl-[18px] flex items-end justify-end ">
        <p class="ml-[9px] text-0.8em tracking-[9px]">
          GROUP
        </p>
      </div>
      <!-- LOGO YOUX -->
    </div>
    <!-- BOTOES DE LOGOUT INFORMACOES DE NOME E CARGO -->
    <div v-if="user" class="flex items-center mr-[10%] font-bold">
      <p class="text-gray-500">
        Nome: <span class="text-youx ml-5 capitalize">{{ user.name }}</span>
      </p>
      <p class="ml-10 text-gray-500">
        Cargo: <span class="text-youx ml-5 capitalize">{{ user.position }}</span>
      </p>
      <p class="cursor-pointer ml-20 text-gray-500" @click="userLeave()">
        Sair
      </p>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      user: undefined,
    }
  },
  watch: { /* Escuta a mudanças nas rotas e verifica se o usuario está logado: remove ou adiciona nome e botão de logout */
    $route(to, from) {
      const user = JSON.parse(localStorage.getItem('user-info'))
      if (user) {
        this.user = {
          name: user.name,
          position: user.function,
        }
      }
      else {
        this.user = undefined
      }
    },
  },
  mounted() { /* Ao montar a pagina, verifica se o usuario está logado: remove ou adiciona nome e botão de logout */
    const user = JSON.parse(localStorage.getItem('user-info'))
    if (user) {
      this.user = {
        name: user.name,
        position: user.function,
      }
    }
  },
  methods: {
    userLeave() { /* Função de disconect do user, limpa o storage, e redireciona para o login */
      localStorage.clear()
      this.$router.push('/login')
    },
    goHome() { /* Link para pagina inicial caso clique em YOUX GROUP no header */
      this.$router.push('/')
    },
  },
}
</script>

<style scoped>
    @import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');
    .tipFont{
        font-family: 'Roboto', sans-serif;
    }
</style>
