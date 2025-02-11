<template>
  <div class="layout__content">
    <div class="layout__title">
      <h1 class="title title--big">Мои данные</h1>
    </div>
    <div
      v-if="user"
      class="user"
    >
      <picture>
        <img
          :src="user.avatar"
          :alt="user.name"
          width="72"
          height="72"
        />
      </picture>
      <div class="user__name">
        <span>{{ user.name }}</span>
      </div>
      <p class="user__phone">
        Контактный телефон: <span> {{ user.phone }}</span>
      </p>
    </div>
    <div class="layout__address">
      <div
        v-for="(item, key) in addresses"
        :key="key"
        class="sheet address-form"
      >
        <div class="address-form__header">
          <b>{{ item.name }}</b>
          <a href="#edit">
            <div class="address-form__edit">
              <button
                type="button"
                class="icon"
                @click="editAddress(item)"
              >
                <span class="visually-hidden">Изменить адрес</span>
              </button>
            </div>
          </a>
        </div>
        <p>{{ item.street }} д.{{ item.building }} кв.{{ item.flat }}</p>
        <small>{{ item.comment }}</small>
      </div>
    </div>

    <article
v-if="isAddition || isEdit"
id="edit"
class="layout__address">
      <div
v-if="isEdit"
class="address-form__header">
        <b>{{ localAddress.name }}</b>
      </div>
      <div class="address-form__wrapper">
        <div class="address-form__input">
          <label class="input">
            <span>Название адреса*</span>
            <input
              v-model="localAddress.name"
              type="text"
              name="addr-name"
              placeholder="Введите название адреса"
              required
            />
          </label>
        </div>
        <div class="address-form__input address-form__input--size--normal">
          <label class="input">
            <span>Улица*</span>
            <input
              v-model="localAddress.street"
              type="text"
              name="addr-street"
              placeholder="Введите название улицы"
              required
            />
          </label>
        </div>
        <div class="address-form__input address-form__input--size--small">
          <label class="input">
            <span>Дом*</span>
            <input
              v-model="localAddress.building"
              type="text"
              name="addr-house"
              placeholder="Введите номер дома"
              required
            />
          </label>
        </div>
        <div class="address-form__input address-form__input--size--small">
          <label class="input">
            <span>Квартира</span>
            <input
              v-model="localAddress.flat"
              type="text"
              name="addr-apartment"
              placeholder="Введите № квартиры"
            />
          </label>
        </div>
        <div class="address-form__input">
          <label class="input">
            <span>Комментарий</span>
            <input
              v-model="localAddress.comment"
              type="text"
              name="addr-comment"
              placeholder="Введите комментарий"
            />
          </label>
        </div>
      </div>

      <div class="address-form__buttons">
        <AppButton
          v-if="isEdit"
          class="button--transparent"
          @click="deleteAddress"
          >Удалить</AppButton
        >
        <AppButton
            class="button--transparent"
            @click="cancel"
          >
          Отмена
        </AppButton
        >
        <AppButton
          name="save-button"
          @click="saveAddress"
        >
          Сохранить
        </AppButton>
      </div>
    </article>
    <div class="layout__button">
      <AppButton
        v-if="!isAddition && !isEdit"
        class="button--border"
        name="add-button"
        @click="addAddress"
      >
        Добавить новый адрес
      </AppButton>
    </div>
  </div>
</template>

<script>
import AppButton from "@/common/components/AppButton";
import { mapState } from "vuex";
import { cloneDeep } from "lodash";
import auth from "@/middlewares/auth";

export default {
  name: "Profile",

  middlewares: { middlewares: auth },

  layout: "ProfileLayout",

  components: {
    AppButton,
  },

  data: function () {
    return {
      isEdit: false,
      isAddition: false,
      localAddress: {
        name: "",
        street: "",
        building: "",
        flat: "",
        comment: "",
      },
    };
  },

  computed: {
    ...mapState("Auth", ["user"]),
    ...mapState("Address", ["addresses"]),
  },

  methods: {
    addAddress() {
      this.clearForm();
      this.isEdit = false;
      this.isAddition = true;
    },

    editAddress(data) {
      this.isAddition = false;
      this.isEdit = true;
      this.localAddress = cloneDeep(data);
      this.localAddress.userId = this.user.id;
    },

    clearForm() {
      Object.keys(this.localAddress).forEach((item) => {
        this.localAddress[item] = "";
      });
    },

    cancel() {
      this.isAddition = false;
      this.isEdit = false;
    },

    createAddress() {
      const data = cloneDeep(this.localAddress);
      if (data.flat === "") {
        data.flat = null;
      }
      if (!data.name) {
        data.name = data.street + " д." + data.building;
        if (data.flat) {
          data.name += " кв." + data.flat;
        }
      }
      this.$store.dispatch("Address/saveAddresses", data);
      this.isAddition = false;
    },

    deleteAddress() {
      const addressId = this.localAddress.id;
      this.$store.dispatch("Address/deleteAddresses", addressId);
      this.isEdit = false;
    },

    edit() {
      this.$store.dispatch("Address/editAddresses", this.localAddress);
      this.isEdit = false;
    },

    saveAddress() {
      if (this.isAddition) {
        this.createAddress();
      } else {
        this.edit();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/scss/blocks/user.scss";
@import "~@/assets/scss/blocks/icon.scss";
@import "~@/assets/scss/blocks/address-form.scss";
@import "~@/assets/scss/blocks/title.scss";
@import "~@/assets/scss/blocks/input.scss";
</style>
