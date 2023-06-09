<template>
  <div id="app">
    <main>
      <EmployeeComponent></EmployeeComponent>
    </main>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import EmployeeComponent from "@/components/EmployeeComponent.vue";
export default defineComponent({
  name: "App",
  components: {
    EmployeeComponent,
  },
});
</script>

<style lang="scss" scoped>
@import "@/assets/scss/style.scss";
</style>
