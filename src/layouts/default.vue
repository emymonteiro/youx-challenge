<!-- Layout para a aplicação SFC -->
<template>
  <main class="text-center text-gray-700 dark:text-gray-200">
    <Header />
    <div class="bg-white flex flex-col items-center mt-10 w-150 mx-auto rounded-md">
      <router-view />
    </div>
  </main>
</template>
