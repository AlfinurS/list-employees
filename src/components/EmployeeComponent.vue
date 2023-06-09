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
        <button @click="showCreateModal" class="btn btn-primary">
          Добавить
        </button>
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
      <div class="contact__compensator"></div>
    </div>

    <!-- компонент формы, который эмитит события редактирования и удаления контакта-->
    <ItemContact
      v-for="contact in filteredContacts"
      :key="contact.id"
      @deleteContact="deleteContact"
      @editContact="showContactModal"
      :dataProps="contact"
    ></ItemContact>

    <!-- компонент с формой в модальном окне для создания нового контакта и редактирвания выбранного -->
    <!-- задаем variant отрисовки модального окна Редактировать или Создать -->
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
  //Родительский компонент, отвечает за обработку данных сотрудников.
  //Отрисовывает компоненты списка и модальное окно создания / редактирования
  name: "EmployeeComponent",
  components: {
    ItemContact,
    ContactModal,
  },

  data() {
    return {
      search: "",
      //массив объектов сотрудников
      contacts: [
        {
          id: 1,
          name: "Ivan",
          surname: "Ivanovich",
          experience: 10,
          age: "60",
          address: "Vologda",
        },
        {
          id: 2,
          name: "Vasia",
          surname: "Vasilievitch",
          experience: 1,
          age: "30",
          address: "Tula",
        },
      ] as contactType[],
      //данные для модального окна
      dataContactModal: {} as contactType,
      //вариант модального окна создать / редактировать
      modalVariant: "create",
    };
  },

  computed: {
    /* метод поиска сотрудников */
    filteredContacts(): contactType[] {
      if (this.search !== "") {
        //если поиск не пустой, то с помощью метода filter через регулярное выражение найти сотрудника
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
    //метод вызывающий модальное окно для редактирования контакта
    showContactModal(contact: contactType) {
      //записываем данные, чтобы пробросить их через пропс в модальное окно
      this.dataContactModal = contact;
      //задаём вариант модального окна - редактировать
      this.modalVariant = "edit";
      //сохраняем ссылку на экземпляр модального окна в константу
      const contactModalRef = this.$refs.contactModalRef as any;
      //через ссылку на компонент открываем модальное окно
      contactModalRef.showModal();
    },

    //деструктуризируем данные, переданные из модального окна и вызываем функцию, соответствующую варианту модального окна
    saveData({ data, variant }) {
      if (variant === "edit") {
        this.editContact(data);
      } else {
        this.addContact(data);
      }
    },

    addContact(contact) {
      this.contacts.push(contact);
      this.saveLocalStorage();
    },

    //для создания нового контакта(сотрудника) вызывается модальное окно
    showCreateModal() {
      //создаём объект для нового контакта
      const newContact: contactType = JSON.parse(JSON.stringify(contactConst));
      //данные нового сотрудника сохраняются в newContact и с помощью метода Date.now присваевается id, чтобы id не дублировались
      newContact.id = Date.now();
      //записываем данные для нового контакта в переменную, которая пробрасывается пропсом в модальное окно
      this.dataContactModal = newContact;
      this.modalVariant = "create";
      const contactModalRef = this.$refs.contactModalRef as any;
      contactModalRef.showModal();
    },

    //редактируем данные сотрудника
    editContact(contact: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === contact.id;
      });
      this.contacts[index] = JSON.parse(JSON.stringify(contact));
      this.saveLocalStorage();
    },

    //удаляем сотрудника из списка
    deleteContact(contact: contactType) {
      const index = this.contacts.findIndex((item) => {
        return item.id === contact.id;
      });
      this.contacts.splice(index, 1);
      this.saveLocalStorage();
    },
    //запускаем метод для сохранения значений в localStorage
    saveLocalStorage() {
      localStorage.setItem("contacts", JSON.stringify(this.contacts));
    },
  },

  //при создании компонента получаем из localStorage данные сотрудников и записываем в переменную, чтобы отрисовать список
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
