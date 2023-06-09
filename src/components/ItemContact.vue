<template>
  <div class="list">
    <div class="contact__wrapper">{{ dataProps.surname }}</div>
    <div class="contact__wrapper">{{ dataProps.name }}</div>
    <div class="contact__wrapper">{{ dataProps.experience }}</div>
    <div class="contact__wrapper">{{ dataProps.age }}</div>
    <div class="contact__wrapper">{{ dataProps.address }}</div>

    <div @click="deleteContact" class="button__wrapper link">
      <iconDelete></iconDelete>
    </div>
    <div @click="editContact" class="button__wrapper link">
      <iconEdit></iconEdit>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import { contactType } from "@/types/common";
import iconDelete from "@/components/icons/iconDelete.vue";
import iconEdit from "@/components/icons/iconEdit.vue";

export default defineComponent({
  name: "ItemContact",
  components: { iconDelete, iconEdit },
  props: {
    dataProps: {
      type: Object as PropType<contactType>,
      default: () => ({}),
    },
  },

  emits: ["deleteContact", "editContact"],

  data() {
    return {};
  },

  methods: {
    editContact() {
      this.$emit("editContact", this.dataProps);
    },

    deleteContact() {
      this.$emit("deleteContact", this.dataProps);
    },
  },
});
</script>

<style lang="scss" scoped>
@import "@/assets/scss/style.scss";
</style>
