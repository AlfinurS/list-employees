<template>
  <div id="app">
    <main>
      <Contact></Contact>
    </main>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Contact from "@/components/Contact.vue";
export default defineComponent({
  name: "App",
  components: {
    Contact,
  },
});
</script>

<style lang="scss" scoped>
@import "@/assets/scss/style.scss";
</style>
