<template>
  <div class="phone-book">
    Список сотрудников
    <div class="header">
      <form action="#" class="search__form">
        <input
          type="text"
          class="search__input"
          v-model="search"
          placeholder="Поиск сотрудников"
        />
        <button class="search__btn" type="submit">Поиск</button>
      </form>
      <div class="buttons__wrapper">
        <button @click="addContact" class="btn btn-primary">Добавить</button>
        <div class="buttons__wrapp">
          <button @click="download" class="btn btn-plain buttons__download">
            Скачать&nbsp;<iconDownload></iconDownload>
          </button>
          <label class="mb-0 w-100" for="file">
            <div class="btn btn-plain">
              Загрузить &nbsp;<iconUpload></iconUpload>
            </div>
          </label>
          <input
            :key="uploadKey"
            class="visibility-none"
            name="file"
            type="file"
            id="file"
            ref="file"
            @change="uploadFile()"
          />
        </div>
      </div>
    </div>

    <div class="contact">
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
    <FormContact
      @deleteContact="deleteContact"
      @sendData="setData"
      v-for="contact in filteredContacts"
      :key="contact.id"
      :dataProps="contact"
    ></FormContact>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import FormContact from "@/components/FormContact.vue";
import { contactType } from "@/types/common";
import { contactConst } from "@/constants/common";
import iconDownload from "@/components/icons/iconDownload.vue";
import iconUpload from "@/components/icons/iconUpload.vue";

export default defineComponent({
  name: "Contact",
  components: {
    FormContact,
    iconDownload,
    iconUpload,
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
          age: "+79998114499",
          address: "ivan@mail.ru",
        },
        {
          id: 2,
          name: "Vasia",
          surname: "Vasilievitch",
          experience: 1,
          age: "+79999999999",
          address: "vasia@mail.ru",
        },
      ] as contactType[],
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
    addContact() {
      const newContact: contactType = JSON.parse(JSON.stringify(contactConst));
      newContact.id = Date.now();
      this.contacts.push(newContact);
      this.saveLocalStorage();
    },

    setData(form: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === form.id;
      });
      this.contacts[index] = JSON.parse(JSON.stringify(form));
      this.saveLocalStorage();
    },

    deleteContact(form: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === form.id;
      });
      this.contacts.splice(index, 1);
      this.saveLocalStorage();
    },

    saveUploadedData(data) {
      const result: any = [];
      data.forEach((item) => {
        const keysString: string = Object.keys(item)[0];
        const valuesString: any = Object.values(item)[0];
        const keysArray: string[] = keysString.split(";");
        const valuesArray: string[] = valuesString.split(";");
        const contact = {};
        keysArray.forEach((key, index) => {
          contact[key] = valuesArray[index];
        });
        result.push(contact);
      });
      this.contacts = [];
      this.contacts = result;
      this.saveLocalStorage();
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
