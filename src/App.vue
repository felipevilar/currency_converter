<template>
  <v-app>
    <v-app-bar app color="#fff" dark>
      <v-app-bar-title>
        <v-img src="@/assets/logo.png" max-width="125"></v-img>
      </v-app-bar-title>
    </v-app-bar>

    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    //
  }),
};
</script>
