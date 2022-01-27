<script setup lang="ts">
const router = useRouter()
</script>

<template>
  <main class="px-4 py-10 text-center dark:text-gray-200">
    <div>
      <p class="text-4xl">
        <carbon-warning class="inline-block" />
      </p>
    </div>
    <router-view />
    <div>
      <button
        class="bg-blue-600 font-bold px-5 py-1 rounded m-3 text-sm mt-8"
        @click="router.back()"
      >
        Voltar
      </button>
    </div>
  </main>
</template>
