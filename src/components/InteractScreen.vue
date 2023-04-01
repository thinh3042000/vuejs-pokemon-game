<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsInteract.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsInteract.length)
        }px`,
      }"
    >
      <card-pokemon
        v-for="(card, index) in cardsInteract"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, value: card }"
        @onFlip="checkRules($event)"
        :cardsInteract="cardsInteract"
      />
    </div>
  </div>
</template>

<script>
import CardPokemon from "./CardPokemon.vue";
export default {
  props: {
    cardsInteract: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardPokemon,
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    checkRules(card) {
      if (this.rules.length === 2) return false;
      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("right");
        // add class disabled
        this.$refs[`card-${this.rules[0].index}`][0].onEnalbeDisabledMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnalbeDisabledMode();
        //reset card
        this.rules = [];
        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElements &&
          disabledElements.length === this.cardsInteract.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        console.log("wrong!");
        setTimeout(() => {
          // close cards
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          //reset
          this.rules = [];
        }, 800);
      } else return false;
    },
  },
};
</script>
<style scoped lang="css">
.screen {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen__inner {
  width: 424px;
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
