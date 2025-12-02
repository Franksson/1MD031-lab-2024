<template>
  <header>
    <div class="header">
      <h1>Welcome to Mc10 Online</h1>
    </div>
  </header>
  <main>
    <section class="menu">
      <h2>Select Item</h2>
      <p>Please select which burger your tummy would find yummy.</p>
      <div class="menu-list">
        <Burger
          v-for="burger in burgers"
          v-bind:burger="burger"
          v-bind:key="burger.name"
          v-on:amountUpdated="addToOrder(burger.name, $event.amount)"
        />
      </div>
    </section>
    <section class="order-info">
      <h2>Customer Information</h2>
      <p>
        Please enter delivery information below so we can find you. And we will
        feed you.
      </p>
      <h3>Delivery Information</h3>
      <p>
        <label for="fullname">Full Name</label>
        <input
          required
          v-model="order.name"
          placeholder="First and last name"
        />
      </p>
      <p>
        <label for="email">Email Address</label>
        <input
          required
          type="email"
          v-model="order.email"
          id="email"
          placeholder="e-mail"
        />
      </p>
      <p>
        <label for="payment">Payment Option</label>
        <select v-model="order.paymentoption" id="payment">
          <option value="Credit Card">Credit Card</option>
          <option value="Swish">Swish</option>
          <option value="Klarna">Klarna</option>
          <option value="Cash">Cash in envelope</option>
        </select>
      </p>
      <fieldset>
        <legend>Select Gender</legend>

        <input type="radio" v-model="order.gender" value="Male" id="male" />
        <label for="male">Male</label>

        <input type="radio" v-model="order.gender" value="Female" id="female" />
        <label for="female">Female</label>

        <input
          type="radio"
          v-model="order.gender"
          value="Non-binary"
          id="nonbinary"
        />
        <label for="nonbinary">Non-binary</label>

        <input type="radio" v-model="order.gender" value="None" id="none" />
        <label for="none">Do not wish to provide</label>
      </fieldset>
    </section>

    <div class="map-container">
      <div id="map" v-on:click="setLocation">
        <div
          class="dot"
          v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }"
        >
          T
        </div>
      </div>
    </div>
    <p>
      <button v-on:click="addOrder()">
        <img src="/img/fragbag.png" alt="" />
        <span>Place order</span>
      </button>
    </p>
  </main>
  <hr />
  <footer>
    <span>&copy; 2025 Mc10 Burger and Beans llc. </span>
  </footer>
</template>

<script>
import Burger from "../components/OneBurger.vue";
import io from "socket.io-client";
import menu from "../assets/menu.json";

const socket = io("localhost:3000");

function MenuItem(name, imgURL, kcal, hasGluten, hasLactose) {
  this.name = name;
  this.imgURL = imgURL;
  this.kcal = kcal;
  this.hasGluten = hasGluten;
  this.hasLactose = hasLactose;
}
export default {
  name: "HomeView",
  components: {
    Burger,
  },
  data: function () {
    return {
      burgers: menu,
      order: {
        name: "",
        email: "",
        paymentoption: "",
        gender: "",
      },
      orderedBurgers: {},
      location: { x: 0, y: 0 },
    };
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },
    addOrder: function () {
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          location: this.location,
          name: this.order.name,
          email: this.order.email,
          payment: this.order.paymentoption,
          gender: this.order.gender,
        },
        orderItems: this.orderedBurgers,
      });
    },
    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top,
      };
      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y,
      };
    },
    addToOrder: function (itemName, amount) {
      this.orderedBurgers[itemName] = amount;
    },
  },
};
</script>

<style>
#map {
  width: 1920px;
  height: 1078px;
  background-image: url("/img/polacks.jpg");
}

.dot {
  position: relative;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}

.map-container {
  width: 50em;
  height: 30em;
  overflow: scroll;
  margin: 1em;
  border: 2px solid gray;
}

body {
  font-family: Arial, Helvetica, sans-serif;
  margin: 0;
}

section {
  margin: 0.5em 1em;
}

.menu {
  border: 2px dashed #000000;
  padding: 1em;
}

.menu-list {
  display: grid;
  grid-template-columns: 33% 33% 33%;
}

.order-info {
  background-color: black;
  color: white;
  border: 2px dashed #ffffff;
  padding: 1em;
}

button {
  margin: 1em 0 0.5em 1em;
  font-size: 1.5em;
}

button:hover {
  background-color: lightblue;
  cursor: pointer;
}

button > img {
  margin-top: auto;
  vertical-align: bottom;
}

header {
  display: flex;
  background-image: url(/img/banner.jpg);
  background-size: 100% auto;
  justify-content: center;
  padding: 0 1em;
  margin: 0.5em 1em;
  height: 20vh;
}

.header h1 {
  color: white;
  margin-top: 2em;
  text-shadow: 1px 1px 2px #000000;
}
</style>
