<template>
  <div class="header-modal">
    Список сотрудников
    <div class="header__wrapp">
      <div class="header__search">
        <form action="#" class="search__form">
          <input
            type="text"
            class="search__input"
            v-model="search"
            placeholder="Поиск сотрудников"
          />
          <button class="search__btn" type="submit">Поиск</button>
        </form>
      </div>

      <div class="">
        <button @click="addContact" class="btn btn-primary">Добавить</button>
      </div>
    </div>
  </div>
  <div>
    <div class="list">
      <div class="contact__wrapper">
        <span class="contact__label">Фамилия</span>
      </div>
      <div class="contact__wrapper">
        <span class="contact__label">Имя</span>
      </div>
      <div class="contact__wrapper">
        <span class="contact__label">Cтаж</span>
      </div>
      <div class="contact__wrapper">
        <span class="contact__label">Возраст</span>
      </div>
      <div class="contact__wrapper">
        <span class="contact__label">Адрес</span>
      </div>
      <div class="contact__compensator"></div>
    </div>
    <ItemContact
      v-for="contact in filteredContacts"
      :key="contact.id"
      @deleteContact="deleteContact"
      @editContact="showContactModal"
      :dataProps="contact"
    ></ItemContact>

    <ContactModal
      ref="contactModalRef"
      @saveData="saveData"
      :dataProps="dataContactModal"
      :variant="modalVariant"
    ></ContactModal>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import ItemContact from "@/components/ItemContact.vue";
import ContactModal from "@/components/ContactModal.vue";
import { contactType } from "@/types/common";
import { contactConst } from "@/constants/common";

export default defineComponent({
  name: "EmployeeComponent",
  components: {
    ItemContact,
    ContactModal,
  },

  data() {
    return {
      search: "",
      contacts: [
        {
          id: 1,
          name: "Ivan",
          surname: "Ivanovich",
          experience: 10,
          age: "60",
          address: "ivan@mail.ru",
        },
        {
          id: 2,
          name: "Vasia",
          surname: "Vasilievitch",
          experience: 1,
          age: "30",
          address: "vasia@mail.ru",
        },
      ] as contactType[],
      dataContactModal: {} as contactType,
      modalVariant: "create",
    };
  },

  computed: {
    filteredContacts(): contactType[] {
      if (this.search !== "") {
        const regexp = new RegExp(this.search, "i");
        const filtered = this.contacts.filter(
          (contact) => regexp.test(contact.name) || regexp.test(contact.surname)
        );
        return filtered;
      }
      return this.contacts;
    },
  },

  methods: {
    /* 
    setData(form: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === form.id;
      });
      this.contacts[index] = JSON.parse(JSON.stringify(form));
      this.saveLocalStorage();
    }, */

    showContactModal(contact: contactType) {
      this.dataContactModal = contact;
      this.modalVariant = "edit";
      const contactModalRef = this.$refs.contactModalRef as any;
      contactModalRef.showModal();
      //this.saveLocalStorage();
    },

    saveData({ data, variant }) {
      if (variant === "edit") {
        this.editContact(data);
      } else {
        this.addContact(data);
      }
      console.log(data, variant);
    },

    addContact(contact) {
      this.showAddContact(contact);
    },

    showAddContact(contact) {
      this.dataContactModal = contact;
      this.modalVariant = "edit";
      const contactModalRef = this.$refs.contactModalRef as any;
      contactModalRef.showModal();

      const newContact: contactType = JSON.parse(JSON.stringify(contactConst));
      newContact.id = Date.now();
      this.contacts.push(newContact);
      console.log(newContact);
      //this.saveLocalStorage(newContact);
    },

    editContact(contact: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === contact.id;
      });
      console.log(contact);
      //this.saveLocalStorage();
    },

    deleteContact(contact: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === contact.id;
      });
      this.contacts.splice(index, 1);
      //this.saveLocalStorage();
    },

    saveLocalStorage() {
      localStorage.setItem("contacts", JSON.stringify(this.contacts));
    },
  },

  created() {
    const value: any = localStorage.getItem("contacts");
    if (value) {
      this.contacts = JSON.parse(value);
    }
  },
});
</script>

<style lang="scss" scoped>
@import "@/assets/scss/style.scss";
</style>
