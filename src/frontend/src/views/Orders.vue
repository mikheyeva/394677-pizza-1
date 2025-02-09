<template>
  <div class="layout__content">
    <div class="layout__title">
      <h1 class="title title--big">История заказов</h1>
    </div>
    <section
      v-for="(order, index) in formatOrders"
      :key="index"
      class="sheet order"
    >
      <div class="order__wrapper">
        <div class="order__number">
          <b>Заказ # {{ order.id }}</b>
        </div>

        <div class="order__sum">
          <span>Сумма заказа: {{ order.total }} ₽</span>
        </div>

        <div class="order__button">
          <AppButton
            class="button--border"
            @click="deleteOrder(order.id)"
            >
            Удалить
          </AppButton>
        </div>
        <div class="order__button">
          <AppButton @click="repeatOrders(order)">Повторить</AppButton>
        </div>
      </div>
      <template v-if="order.orderPizzas">
        <ul
          v-for="pizza in order.orderPizzas"
          :key="pizza.id"
          class="order__list"
        >
          <li class="order__item">
            <div class="product">
              <img
                src="../public/img/product.svg"
                class="product__img"
                width="56"
                height="56"
                :alt="pizza.name"
              />
              <div class="product__text">
                <h2>{{ pizza.name }}</h2>
                <ul>
                  <li v-if="pizza.size && pizza.dough">
                    {{ pizza.size.name }}, {{ pizza.dough.name }}
                  </li>
                  <li v-if="pizza.sauce">Соус: {{ pizza.sauce.name }}</li>
                  <li>
                    Начинка:
                    <span
                      v-for="(ingredient, index) in pizza.ingredientsOrder"
                      :key="index"
                      >{{ ingredient.name }}
                      <span
                        v-if="ingredient.count && ingredient.count > 1"
                        >
                        X {{ ingredient.count }}
                      </span>,
                    </span>
                  </li>
                </ul>
              </div>
            </div>
            <p class="order__price">
              {{ pizza.total / pizza.quantity }} ₽
              <span v-if="pizza.quantity > 1">x {{ pizza.quantity }}</span>
            </p>
          </li>
        </ul>
      </template>
      <ul
        v-if="order.misc"
        class="order__additional"
      >
        <li
          v-for="(misc, index) in order.misc"
          :key="index"
        >
          <img
            :src="misc.image"
            width="20"
            height="30"
            :alt="misc.name"
          />
          <p>
            <span>{{ misc.name }}</span>
            <b>{{ misc.count * misc.price }}₽</b>
          </p>
        </li>
      </ul>
      <p
        v-if="order.orderAddress"
        class="order__address"
      >
        Адрес доставки: ул.{{ order.orderAddress.street }}, д.{{
          order.orderAddress.building
        }},
        <span
          v-if="order.orderAddress.flat"
        >
          кв.{{ order.orderAddress.flat }}
        </span>
      </p>
    </section>
  </div>
</template>

<script>
import AppButton from "@/common/components/AppButton";
import auth from "@/middlewares/auth";
import { mapGetters, mapMutations } from "vuex";
import {
  SET_CART_REPEAT_ORDER,
  CLEAN_CART,
} from "@/store/modules/mutation-types";
import pizzaHistoryOptions from "@/common/mixins/formatOrderOptions";

export default {
  name: "Orders",

  middlewares: { middlewares: auth },

  layout: "ProfileLayout",

  components: {
    AppButton,
  },

  mixins: [pizzaHistoryOptions],

  computed: {
    ...mapGetters("Orders", ["formatOrders"]),
  },

  mounted() {
    this.setInitialData();
  },

  methods: {
    ...mapMutations("Cart", [SET_CART_REPEAT_ORDER, CLEAN_CART]),

    setInitialData() {
      this.$store.dispatch("Builder/getBuilder");
      this.$store.dispatch("Cart/getMisc");
      this.$store.dispatch("Orders/getOrders");
    },

    deleteOrder(id) {
      this.$store.dispatch("Orders/deleteOrders", id);
    },

    repeatOrders(order) {
      this.$router.push("/cart");
      this[CLEAN_CART]();
      this[SET_CART_REPEAT_ORDER](order);
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/scss/blocks/order.scss";
@import "~@/assets/scss/blocks/product.scss";
@import "~@/assets/scss/blocks/title.scss";
</style>
