<template>
  <div class="content__dough">
    <div class="sheet">
      <h2 class="title title--small sheet__title">Выберите тесто</h2>

      <div class="sheet__content dough">
        <label
          v-for="(dough, index) in doughList"
          :key="index"
          class="dough__input"
          :class="'dough__input--' + className(dough)"
        >
          <input
            type="radio"
            name="dough"
            class="visually-hidden"
            :value="dough.class"
            :checked="dough.checked"
            @change="changeDough(index)"
          />
          <b>{{ dough.name }}</b>
          <span>{{ dough.description }}</span>
        </label>
      </div>
    </div>
  </div>
</template>

<script>
import { CHANGE_OPTIONS } from "@/store/modules/mutation-types";
import { mapMutations } from "vuex";

export default {
  name: "BuilderDoughSelector",

  props: {
    doughList: {
      type: Array,
      default() {
        return [];
      },
    },
  },
  methods: {
    ...mapMutations("Builder", [CHANGE_OPTIONS]),

    changeDough(index) {
      this[CHANGE_OPTIONS]({ index: index, name: "dough" });
    },

    className(dough) {
      let name = "";
      if (dough.name === "Тонкое") {
        name = "light";
      } else {
        name = "large";
      }
      return name;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/scss/blocks/dough.scss";
</style>
