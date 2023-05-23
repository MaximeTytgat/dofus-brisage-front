<template>
  <v-layout>
    <v-app-bar class="navBar">
      <div class="w-100 d-flex justify-center">
        <div class="w-50 d-flex justify-space-around">
          <v-btn class="bold" rounded="xl" to="/">Welcome</v-btn>
          <v-btn class="bold" rounded="xl" to="/board">Board</v-btn>
          <v-btn class="bold" rounded="xl" to="/brisage">Brisage</v-btn>
        </div>
      </div>
    </v-app-bar>

    <v-main style="min-height: 100vh; min-width: 100vw;">
      <NuxtPage />
    </v-main>
  </v-layout>
</template>
<style lang="scss" scoped>

.navBar {
  background-color: #000000;
  color: #02B96E;
}
.contentNavBar {
  display: flex;
  justify-content: center;
  font-weight: bolder;
}
.bold {
  font-weight: bolder;
}

</style>