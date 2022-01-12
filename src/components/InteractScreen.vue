<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((1000 - 16 * 4) / Math.sqrt(cardsContext.length) - 8) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flid
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceurl="`img/${card}.png`"
        :card="{ index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>
<script>
import CardFlid from "./Card.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardFlid,
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;

      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value == this.rules[1].value
      ) {
        console.log("Right...");
        //add class 'disable' to component card
        this.$refs[`card-${this.rules[0].index}`][0].onEnabledDisableMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnabledDisableMode();
        //reset rules to []
        this.rules = [];
        const disableElements = document.querySelectorAll(
          ".screen .card.isDisable"
        );
        console.log(disableElements);
        if (
          disableElements &&
          disableElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        console.log("wrong...");
        setTimeout(() => {
          // close cards
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          // reset rules to []
          this.rules = [];
        }, 800);
      } else return false;
    },
  },
};
</script>
<style lang="css" scoped>
.screen {
  width: 100%;
  height: 910px;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen__inner {
  display: flex;
  flex-wrap: wrap;
  margin: 1rem auto;
  justify-content: center;
}
</style>
