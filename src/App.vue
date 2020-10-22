<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center text-h6 text-sm-h3">
        Chronotick
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/CedricBardaine"
        target="_blank"
        text
      >
        <span class="mr-2">CedricBardaine</span>
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <!-- faire le button et graph là  -->
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data: () => ({
  }),
};
</script>
