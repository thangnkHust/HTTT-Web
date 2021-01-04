<template>
  <v-app>
    <v-main class="stylemain">
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>

<style lang="scss" scoped>
.stylemain {
  background-image: url("./assets/924.jpg");
  background-size: cover;
}
</style>
