<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <Calendar msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import Calendar from "@/components/Calendar.vue";

export default {
  name: "Home",
  components: {
    Calendar
  }
};
</script>
