<template>
  <v-app>
    <v-main class="stylemain">
      <Calendar />
    </v-main>
  </v-app>
</template>

<script>
import Calendar from "./components/Calendar";

export default {
  name: "App",

  components: {
    Calendar,
  },

  data: () => ({
    //
  }),
};
</script>

<style lang="scss" scoped>
.stylemain {
  background-image: url("./assets/924.png");
  background-size: cover;
}
</style>
