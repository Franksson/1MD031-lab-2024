<template>
  <div class="menu-item">
    <h3>{{ burger.name }}</h3>
    <img v-bind:src="burger.imgURL" />
    <p>
      <button v-on:click="updateAmount(-1)">-</button>
      <span>{{ amountOrdered }}</span>
      <button v-on:click="updateAmount(1)">+</button>
    </p>
    <ul>
      <li>{{ burger.kcal }} kCal</li>
      <li v-if="burger.hasGluten">
        Contains <span class="allergies">gluten</span>
      </li>
      <li v-if="burger.hasLactose">
        Contains <span class="allergies">lactose</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "OneBurger",
  props: {
    burger: Object,
  },
  data: function () {
    return {
      amountOrdered: 0,
    };
  },
  methods: {
    updateAmount: function (amount) {
      if (this.amountOrdered + amount < 0) {
        amount = -this.amountOrdered;
      }
      this.amountOrdered += amount;
      this.$emit("amountUpdated", { amount: this.amountOrdered });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.allergies {
  font-weight: bold;
}
.menu-item {
  padding: 1em;
  margin: 1em;
}
img {
  width: 10em;
}
</style>
